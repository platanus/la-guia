# Engines - Modularización en Rails

> 💡 Antes de arrancar, quiero aclarar que la siguiente guía habla desde la perspectiva de https://github.com/platanus/nest ya que es el único proyecto donde estamos experimentando con engines.

Para modularizar nest, estamos usando [engines de Rails](https://guides.rubyonrails.org/engines.html).

Se debe tener en cuenta que el concepto es el mismo que el de la [guía de rails](https://guides.rubyonrails.org/engines.html) pero en nest estamos poniendo los módulos/engines dentro del repositorio de la main app.

También es importante aclarar que consideramos la "main app" a todo lo que está directamente bajo el root del proyecto. Por ej: un modelo en `app/models/user.rb` es un modelo de la main app. En cambio un modelo en `engines/recruiting/app/models/recruiting/user.rb` es un modelo del engine de reclutamiento.

## Generador

Para facilitar la tarea de crear nuevos engines usamos [Metagem](https://github.com/platanus/metagem) de la siguiente manera:

```bash
bin/rails g metagem:new_engine engine_name
```

Ejemplo:

```bash
bin/rails g metagem:new_engine recruiting
```

Contesta las preguntas y terminarás teniendo la estructura base de tu engine.

> Si modificas algo en algún engine, por favor traspasa ese conocimiento a Metagem. La idea de tener un generador es que este vaya recogiendo el conocimiento común y así evitar que otros devs tengan que enfrentarse a los mismos problemas.

## Conceptos generales

### Diferencia entre engines y gemas

### Gemas

* Son código encapsulado Ruby. Una librería.

* Se utilizan para extraer alguna funcionalidad específica.

* Se puede utilizar en proyectos que no sean Rails.

* NO tienen código Rails: controllers, modelos, etc.

* Ejemplos de gemas: `httparty`, `remove_bg`, `pry`, etc.

### Engines

* Los engines también son gemas de Ruby.

* Se utilizan para extender Rails.

* Tienen la misma estructura de directorios (app, config, etc) que una app Rails.

* En Platanus las utilizamos para extraer funcionalidades (módulos) completas.

* Ejemplos de engines: `devise`, `paranoia`, `active admin`, etc.

### Estructura

### Main App

Es donde está todo el código que es común a todos los módulos y aquí es donde se conectarán todos los feature engines.

### Feature Engine

Existirán n feature engines. Uno por cada funcionalidad independiente de Platanus.
Los feature engines deben pensarse como **funcionalidad que se puede desconectar de la main app sin romperla.** Por ejemplo el módulo de reclutamiento es un ejemplo de feature engine.

### Testing

Los tests de una funcionalidad de la main app van dentro de /spec en cambio los tests de una funcionalidad de un engine va dentro de engines/engine_name/spec. Por ej: engines/recruiting/spec/models/recruiting/process_spec.rb es el test de un modelo del módulo/engine de reclutamiento

### Initializer

Se utilizan para configurar gemas. Para agregar configuración al engine, primero deberás definir el atributo en `engines/tu_engine/lib/tu_engine.rb`. Por ejemplo: supongamos que queremos pasar un token al engine `SlackUtils`.

Primero agregamos el atributo `api_token` en `engines/slack_utils/lib/slack_utils.rb` así:

```ruby
require "slack_utils/engine"

module SlackUtils
  extend self

  attr_accessor :api_token

  def configure
    yield self
    require "slack_utils"
  end
end
```

Luego, en el initializer del engine (ubicado en `/app/config/initializers/slack_utils.rb` siguiendo el ejemplo):

```ruby
EnginesUtil.initialize_engine(:slack_utils) do |config|
  config.api_token = "XXX"
end
```

Después, dentro del engine, podrás usarlo así:

```ruby
SlackUtils.api_token #=> "XXX"
```

### Referenciar gemas locales en otras gemas/engines locales.

Es común que pase que en un engine que definimos localmente (dentro de la main app) queramos poner como dependencia a otro engine o gema local. Para explicarles cómo se hace supongamos que tengo el engine de reclutamiento (recruiting) que requiere el engine slack_utils:

Primero, en el `Gemfile` de recruiting agregaremos la referencia a la gema así:

```ruby
source '<https://rubygems.org>'
git_source(:github) { |repo| "<https://github.com/#{repo}.git>" }

gemspec

gem "slack_utils", path: "../../engines/slack_utils"
```

> Como se puede ver, se hace una referencia a un path local.

y en el `recruiting.gemspec`:

```ruby
spec.add_dependency "slack_utils"
```

De esta manera, al hacer `bundle install`, el `Gemfile.lock` referenciará al path local en vez de a una gema publicada en rubygems.

> Es importante destacar que las dependencias entre engines se hacen a nivel de gemspec/Gemfile y a través del initializer.

### Particularidades de los Engines

### Ubicación

Los engines se crean dentro de `/engines` y se agregan automáticamente en el `Gemfile` de la app principal.
Los tests van dentro de `engines/tu_engine/spec`

### Isolated namespace

Los engines ejecutan un método `isolate_namespace MyEngine` así:

```ruby
module Recruiting
  class Engine < ::Rails::Engine
    isolate_namespace Recruiting
    # ...
```

Ese método se encarga de aislar controllers, models, routes, etc. dentro de un namespace para evitar colisiones de nombres y overrides. Por ejemplo, al crear un nuevo modelo, este se creará dentro del namespace y la tabla tendrá como prefijo el nombre del engine.

Se creará:

```ruby
module Recruiting
  class ProcessType < ApplicationRecord
    # ...
```

en vez de:

```ruby
class ProcessType < ApplicationRecord
  # ...
```

y la tabla se llamará `recruiting_process_types` en vez de simplemente `process_types`

### Extender clases

Es común que el engine extienda models, controllers, etc. existentes. Estas extensiones se agregan dentro del engine en el directorio que corresponda. Por ejemplo: si dentro del engine fdbk quiero extender el modelo `TeamMember` esa extensión debería ponerla acá: `engines/nombre_engine/app/models/nombre_engine/nombre_modelo_ext.rb` -> `engines/fdbk/app/models/fdbk/team_member_ext.rb`. Si en cambio quiero extender por ej un observer, debería ponerlo acá: `engines/nombre_engine/app/observers/nombre_engine/nombre_observer_ext.rb` -> `engines/fdbk/app/observers/fdbk/team_member_observer_ext.rb`.

Por ej, para extener el modelo `TeamMember` los pasos a seguir son:

1. Hacer que el modelo nos indique que está listo para ser extendido.

    Esto se hace dispatcheando el evento **al final **de la clase definida en `app/models/team_member.rb`

    ```ruby
    class TeamMember < ApplicationRecord
      #...
    
      ActiveSupport.run_load_hooks("TeamMember", self)
    end
    ```

1. Definir la extensión.

    Como es un modelo, debo agregarla acá: `engines/tu_engine/app/models/tu_engine/team_member_ext.rb` y se ve algo así:

    ```ruby
    module TuEngine
      module TeamMemberExt
        extend ActiveSupport::Concern
    
        included do
          def hola
            puts "soy un método que agregó el engine"
          end
        end
      end
    end
    ```

1. Extender la funcionalidad.

    Se hace dentro de `engines/tu_engine/lib/tu_engine/extensions.rb`

    ```ruby
    ActiveSupport.on_load("TeamMember") do
      include TuEngine::TeamMemberExt
    end
    ```

    El código anterior indica que una vez que se ejecute el evento `TeamMember`, extienda la funcionalidad del modelo `TeamMember` incluyendo la extensión `TuEngine::TeamMemberExt`.

Pasa saber si una funcionalidad va dentro de la main app o de una extensión tienen que imaginarse que ponen ese método en la main app y contestarse la siguiente pregunta: "si borrara el engine el método que quedó en la main app sigue funcionando o se rompe". Si la respuesta es: "se rompe" es que va en una extensión. Por ej: supongamos que agrego la siguiente relación a `TeamMember`:

```ruby
class TeamMember < ApplicationRecord
  has_many :fdbk_schedules, class_name: "::Fdbk::Schedule", dependent: :nullify
end
```

Como ven es una relación que apunta a una tabla del engine Fdbk. Entonces me hago la pregunta: "¿Si borro el engine Fdbk esa relación se rompe?" la respuesta en este caso sería que sí ya que al borrar el engine se borraría la tabla `fbdk_schedules` y por lo tanto se rompería la relación. Para que esto no suceda, la extensión se coloca dentro del engine Fdbk y si el día de mañana se borrara (junto a la tabla `fbdk_schedules`) se borraría también la relación `has_many :fdbk_schedules` y todo (lo de la main app) quedaría intacto.

### Activar código de un feature engine en la main app (core)

Para eso se puede utilizar el helper `EnginesUtil`.

```ruby
# como bloque
EnginesUtil.with_available_engine(:nombre_de_tu_engine) do
  # ejecuto acá código que debe correrse solo si el engine está cargado
end
```

```ruby
# como condicional
if EnginesUtil.available_engine?(:nombre_de_tu_engine)
  # ejecuto acá código que debe correrse solo si el engine está cargado
end
```

Ejemplo:

```ruby
EnginesUtil.with_available_engine(:recruiting) do
  # ...
end
```

En general cuando estoy trabajando en un engine debo intentar por todos los medios poner vistas, modelos, etc. dentro del engine. El problema es que muchas veces eso no se podría hacer. Supongamos el caso de una navbar que vive en la main app. Si quiero agregar un link a alguna vista de reclutamiento no me queda otra que agregarlo dentro de la main app, no? Bueno, en casos como estos se puede usar los métodos mencionados anteriormente. Por ej: en el dashboard de active admin que vive dentro de la main app, se agrega info de los diferentes engines solo si están activos. Este es el código:

```ruby
ActiveAdmin.register_page "Dashboard" do
  menu priority: 1, label: proc { I18n.t("active_admin.dashboard") }


  content title: proc { I18n.t("active_admin.dashboard") } do
    panel I18n.t("active_admin.pages.dashboard.search_platanus_github") do
      render partial: 'search_github_form'
    end

    if EnginesUtil.available_engine?(:recruiting)
      render("admin/dashboard/recruiting_processes")
    end

    if EnginesUtil.available_engine?(:fdbk)
      render("admin/dashboard/fdbk_sessions")
    end

    panel I18n.t("active_admin.pages.dashboard.tasks") do
      render partial: 'tasks'
    end
  end
end
```

Con el código anterior, si se borrara el engine de reclutamiento, esta vista `admin/dashboard/recruiting_processes` se borarría también. Si no existiera la condición `if EnginesUtil.available_engine?(:recruiting)` daría un error. Como si existe la condición, simplemente no se mostrará info relacionada al reclutamiento y ya.

### Agregar modelos, controllers, etc.

Los engines tienen la misma estructura que una rails app. Entonces si quieres que tu engine agregue por ejemplo un job, harás lo mismo que harías en la main app pero dentro del engine. Es decir, lo agregarás dentro de: `/engines/recruiting/app/jobs/recruiting/assignation_job.rb`

```ruby
module Recruiting
  class AssignationJob < Recruiting::ApplicationJob
    # ...
  end
end
```

Ten en cuenta que:

1. Tu job heredará de `TuEngine::ApplicationJob`. En este caso: `Recruiting::ApplicationJob`

1. Se definirá dentro del namespace `TuEngine`. En este caso: `Recruiting`.

> Lo 2 puntos anteriores se aplican para controllers, modelos, etc.

### Creación de modelos y migraciones

Los modelos para los engines se crean ejecutanto el siguiente comando en el root del proyecto:

```bash
bin/rails g model engine_name/model_name --engine-model
```

Como se puede ver, la única diferencia con la creación de un modelo normal de Rails es la opción `--engine-model`

Por ej:

```bash
bin/rails g model guides/media_file type:string name:string identifier:string file_data:text section:references  --engine-model
```

Algo a tener en cuenta es que, como metagem está en desarrollo, hoy en día los modelos colocan las migraciones donde corresponde pero si luego creamos más migraciones, estas se crearán en la main app y deberemos moverlas a mano a `engines/nombre_engine/db/migrate/xxx.rb` -> ej: `engines/guides/db/migrate/20220927140455_create_guides_sections.rb`

### Rutas

Las rutas de un engine se definen dentro del engine pero, para que estén disponibles dentro de la main app, deberás montarlas en `/config/routes.rb` así:

```ruby
Rails.application.routes.draw do
  mount Recruiting::Engine, at: '/recruiting'

  #...
```

En el engine `/engines/recruiting/config/routes.rb`

```ruby
Recruiting::Engine.routes.draw do
  get  '/dashboard' => 'dashboard#index'
  root 'dashboard#index'
end
```

Un detalle importante a considerar es que desde main app se tiene acceso a los helpers de los engines disponibles según su nombre de ruta que se puede obtener con los comandos anteriores. Por ejemplo:

```ruby
# app/views/shared/navbar/_right_menu.html.erb (desde main_app)

link_to recruiting.recruiting_index_url
```

Y, también, desde los engines, se tiene acceso a `main_app`, con lo que se tiene acceso directo a los helpers de rutas del `main_app`. Por ejemplo:

```ruby
# engines/recruiting/app/views/recruiting/shared/_main_header.html.erb (desde recruiting)

link_to "Ingresar", main_app.recruiting_new_user_session_path
```

### Testing

* Al ejecutar `export ENABLED_ENGINES='' bin/rspec spec` se correrán **solo los tests de la main app con todos los engines prendidos**.

* Al ejecutar `export ENABLED_ENGINES=false bin/rspec spec` se correrán **solo los tests de la main app con todos los engines apagados**.

* Al ejecutar `export ENABLED_ENGINES='engine_name' bin/rspec engines/engine_name/spec` -> ej: `export ENABLED_ENGINES='recruiting' bin/rspec engines/recruiting/spec` se correrán los **test de un engine específico**. En este caso los de reclutamiento.

* Al ejecutar `export ENABLED_ENGINES='guides' bin/rspec engines/recruiting/spec` **veremos el error ****`uninitialized constant Recruiting`** porque estamos tratando de correr los tests para reclutamiento activando solo el engine `guides`.

Quizás la forma más cómoda de correr los tests localmente es dejar la variable de entorno seteada en vacío -> `ENABLED_ENGINES=''` para que estén todos los engines habilitados ya que igual, en CircleCI, los tests correrán de manera independiente para asegurar que no tienen dependencias indebidas.

Si hacemos esto último entonces:

* Al ejecutar `bin/rspec spec` se correrán **solo los tests de la main app con todos los engines prendidos.**

* Al ejecutar `bin/rspec engines/recruiting/spec` se correrán **solo los tests del engine de reclutamiento con todos los engines prendidos.**

También puedes usar `bin/guard` para que los tests corran cuando modifiques las clases o sus tests. Recuerda tener `ENABLED_ENGINES=''`.

## Front related

Las siguientes secciones no están muy chequeadas ya que todavía estamos explorando el tema modularización y engines. Solo se han dejado en esta guía como futura referencia.

### Webpacker

Cada `engine` debe tener su propio pack en `main_app`, como, por ejemplo:

* `app/javascript/packs/recruiting/recruiting.js`

Ahí, debe definir las siguientes cosas:

* Dependencias JS definidas en `package.json` de `main_app`

* Punto de entrada a SCSS definidos en `javascript/stylesheets` del `engine`

* Punto de entrada a Assets definidos en `javascript/assets` del `engine`

* Componentes Vue definidos en `javascript/components` del `engine`

Es bueno definir un custom alias del directorio `javascript` del `engine`:

```javascript
// config/webpack/custom.js

'@recruiting': path.resolve(__dirname, '..', '..', 'engines/recruiting/app/javascript')
```

Para así, luego usar de forma más limpia en el Pack del `engine`:

```javascript
// Pack: app/javascript/packs/recruiting/recruiting.js (partial)
import '@recruiting/stylesheets/recruiting/recruiting';

Vue.component('component-name', () => import('@recruiting/components/recruiting/component-name'));
```

```javascript
// Pack: app/javascript/packs/recruiting/recruiting.js (partial)
require.context('@recruiting/assets/recruiting', true);
```

### Componentes Vue

Los componentes Vue deben quedar en la carpeta `javascript/components` del `engine`.

Ejemplo:

* `engines/recruiting/app/javascript/components/recruiting`

Y deben ser referenciados desde su Pack particular como se mencionó anteriormente.

### SCSS

Un detalle importante al escribir SCSS, es utilizar apropiadamente las referencias a assets.

Por ejemplo, para usar `url()` debes considerar lo siguiente:

* Si el asset está en el `engine`, debes usar un path relativo, como: `url('../../path/to/asset.png')`

* Pero, si el asset está en `main_app`, debes usar este tipo de path partiendo desde `app/javascript` como referencia: `url('~path/to/asset.png')`

### Assets

Assets en el pipeline de Sprockets, se pueden usar directamente igual que en `main_app`.
Siguiendo la misma estructura de directorio y configurando el `initializer` respectivo.

Como ejemplo, considera el caso de Discovery:

* `engines/recruiting/config/initializers/assets.rb`

* `engines/recruiting/app/assets/images/defaults/recruiting/topic/image.png`
