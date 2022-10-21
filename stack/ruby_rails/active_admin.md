# Active Admin

Es una gema que utilizamos como back office.

## ¿Por qué la usamos?

La utilizamos porque resuelve rápidamente los típicos CRUD de admin. Con ActiveAdmin en unos minutos puedes tener para un recurso (modelo ActiveRecord): el menu para acceder, las vistas para crear/editar, el listado paginado con filtros y varias cosas más.

## ¿Cómo la usamos?

### Instalación

La gema viene instalada si el proyecto se generó usando [Potassium](https://github.com/platanus/potassium). Si no es así, igual puedes agregarlo luego ejecutando `potassium install admin`.

### Uso básico

Supongamos que tenemos el modelo `Blog` con los atributos `title`, `body` y `user` (owner del Blog). Si agregamos bajo `/app/admin/blogs.rb` el siguiente código:

```ruby
ActiveAdmin.register Blog do
end
```

Al acceder a `http://localhost:3000/admin/blogs` veremos el listado de blogs:

<img src='assets/active-admin-1.png'/>

y si hacemos clic en el link "Editar" de alguno de los blogs veremos el formulario:

<img src='assets/active-admin-2.png'/>

Listo! Eso es todo lo que necesitas hacer para tener algo funcionando. De todos modos, en un proyecto Platanus comúnmente verás algo como esto:

```ruby
ActiveAdmin.register Blog do
  permit_params :title, :body, :user_id

  filter :title

  index do
    selectable_column
    id_column
    column :title
    column :user
    actions
  end

  show do
    attributes_table do
      row :title
      row :body
      row :user
    end
  end

  form do |f|
    f.semantic_errors

    f.inputs do
      f.input :title
      f.input :body
      f.input :user
    end

    f.actions
  end
end
```

para tener más control de lo que se quiere mostrar. Por ejemplo, así se ve el index con la configuración custom:

<img src='assets/active-admin-3.png'/>

### I18n

Active Admin toma la configuración de locales de Rails para traducir los nombres de las columnas.
Por ejemplo, para traducir los atributos de `Blog` deberías tener la siguiente configuración:

`/config/locales/es-CL.yml`

```yaml
es-CL:
  activerecord:
    attributes:
      blog:
        title: Título
        body: Texto
        user: Dueño
```

De esta manera al entrar por ejemplo la vista de un `Blog` verás los atributos traducidos:

<img src='assets/active-admin-4.png'/>

Ten en cuenta que también funciona con métodos (getters) custom. Por ejemplo, podrías tener:

`/app/models/blog.rb`

```ruby
class Blog < ApplicationRecord
  def my_method
    "Hola!"
  end
end
```

`/config/locales/es-CL.yml`

```yaml
es-CL:
  activerecord:
    attributes:
      blog:
        my_method: Mi método
```

`/app/admin/blogs.rb`

```ruby
ActiveAdmin.register Blog do
  index do
    selectable_column
    id_column
    column :my_method
    actions
  end

  show do
    attributes_table do
      row :my_method
    end
  end
end
```

y funcionará.

### Menú

**Agrupar recursos**

Para agrupar varios items dentro de un mismo menú, debes:

1. Definir el menú en el initializer de active admin:

    `/config/initializers/active_admin.rb`

    ```ruby
    ActiveAdmin.setup do |config|
      config.namespace :admin do |admin|
        admin.build_menu do |menu|
          menu.add id: :some_menu_id, label: "Grupo"
        end
      end
    end
    ```

1. Definir en el recurso su menú padre:

    `/app/admin/blogs.rb`

    ```plain text
    ActiveAdmin.register Blog do
      menu parent: :some_menu_id
    end
    ```

    <img src='assets/active-admin-5.png'/>

**Ocultar menú**

Se hace de la siguiente manera:

`/app/admin/blogs.rb`

```ruby
ActiveAdmin.register Blog do
  menu false
end
```

A simple vista parece no tener mucha utilidad pero lo importante aquí es que aunque no exista el menú, igual existen los endpoints. Algo que puede ser conveniente si queremos usar alguna ruta del admin como una API.

<img src='assets/active-admin-6.png'/>

**Menú condicional**

Puede ser útil mostrar u ocultar un menú dependiendo de una condición. El caso más típico es el de roles. Por ejemplo:

`/app/admin/blogs.rb`

```ruby
ActiveAdmin.register Blog do
  menu if: -> { current_admin_user.super_admin? }
end
```

### Permisos (Pundit)

En Platanus usamos Active Admin con el [adapter de Pundit](https://activeadmin.info/13-authorization-adapter.html#using-the-pundit-adapter) para autorizar recursos.
Si al registrar un nuevo recurso en AA, no tienes creado el policy de ese recurso, observarás un error así:

<img src='assets/active-admin-7.png'/>

Si esto ocurre, agrega el policy correspondiente y define los permisos para cada una de las acciones del CRUD:

```ruby
class BlogPolicy < ApplicationPolicy
  def index?
    true
  end

  def show?
    true
  end

  def create?
    true
  end

  def new?
    create?
  end

  def update?
    true
  end

  def edit?
    update?
  end

  def destroy?
    true
  end
end
```

> Si todavía no estás en la instancia del proyecto en la cual tienes que preocuparte por permisos deja todas las acciones en true.

Es importante mencionar que si una acción no tiene permisos, esta desaparecerá del menú y links, etc. Por ejemplo:

```ruby
class BlogPolicy < ApplicationPolicy
  def show?
    false
  end
end
```

Al tratar de acceder a `http://localhost:3000/admin/blogs/303`

<img src='assets/active-admin-8.png'/>

Se puede ver además como el link a "Ver" desapareció:

<img src='assets/active-admin-9.png'/>

### Action Items

Los `action_item`s son botones que se pueden agregar a las vistas del recurso. Por ejemplo, el siguiente código mostrará un botón (link), solo en la vista `index`, para ir al listado de administradores.

```ruby
action_item :go_to_admins, only: [:index] do
  link_to("Ver Administradores", admin_admin_users_path)
end
```

<img src='assets/active-admin-10.png'/>

> Ten en cuenta que puedes decidir en qué vistas aparecerá el botón usando la opción only.

### Member Action

Las `member_action`s son acciones extra que se pueden agregar al controller del recurso. Por ejemplo, el siguiente código:

```ruby
member_action :send_mail, method: :post do
  # Ejecuta algún código. Por ejemplo enviar el blog por mail a n usuarios.

  redirect_to admin_blogs_path
end
```

sumará el endpoint `/admin/blogs/:id/send_mail` a los endpoints del CRUD.

<img src='assets/active-admin-11.png'/>

Ten en cuenta que se puede utilizar `action_item`s para ejecutar estas nuevas acciones. Por ejemplo, el siguiente código agregará un botón en la vista del blog (`show`) desde el que se llamará a la acción `send_mail`.

```ruby
action_item :send_mail, only: [:show] do
  link_to("Enviar a Admin Users", send_mail_admin_blog_path(resource), method: :post)
end
```

Una alternativa a los `action_item`s es agregar la acción al listado del index así:

```ruby
index do
  # ...
  actions do |blog|
    link_to("Enviar", send_mail_admin_blog_path(blog), method: :post)
  end
end
```

<img src='assets/active-admin-12.png'/>

### Collection Action

Es lo mismo que una `member_action` pero sin apuntar a un recurso en particular sino a la colección. Por ejemplo, la siguiente `member_action`:

```ruby
member_action :send_mail, method: :post do
  # ...
end
```

creará el siguiente endpoint: `POST /admin/blogs/:id/send_mail`. En cambio, esta `collection_action`:

```ruby
collection_action :send_mails, method: :post do
  # ...
end
```

generará este: `POST /admin/blogs/send_mails`

La idea entonces con esto es que las `member_action`s se usen junto a `action_item`s en `show`, `new` y `update` y las `collection_action` con `action_item`s en el `index`.

> Ten en cuenta que dentro de una member_action podrás acceder al recurso actual (en nuestro ejemplo un Blog de id x) usando el método resource. En cambio, en una collection_action, podrás acceder al listado de recursos (lista de Blogs en el ejemplo), que está mostrando el index en ese momento, usando el método collection.

### Display Name

Si prestaste atención a la imagen del formulario de la sección "Uso básico", seguro notaste que el selector de usuarios no muestra correctamente el nombre de los mismos:

<img src='assets/active-admin-13.png'/>

esto se debe a que Active Admin espera que los recursos tengan definido el método: `:display_name` para que puedan ser representados como "String". Si el recurso no lo implementa, simplemente llamará a `to_s` mostrando como resultado lo que vemos en el selector. Entonces, para solucionar esto, podemos hacer lo siguiente:

```ruby
class User < ApplicationRecord
  def display_name
    email
  end
end
```

<img src='assets/active-admin-14.png'/>

> Ten en cuenta que display_name (o cualquiera de las otras opciones) se utilizará en varios lugares: en el título de un recurso, en los links de las rows/columns y, como vimos, en los selectores.

### Vistas custom

Hay veces que necesitamos agregar nuevos endpoints con HTML a medida. Para hacer esto hacer lo siguiente:

1. Agregar la `member_action`:

    ```plain text
    member_action :preview, method: :get do
      @blog = resource
    end
    ```

1. Agregar el `action_item`:

    ```plain text
    action_item :preview, only: [:show] do
      link_to("Previsualizar", preview_admin_blog_path(resource))
    end
    ```

1. Agregar la vista custom:

    Agregamos el HTML para nuestra nueva `member_action` en `/app/views/admin/blogs/preview.html.erb`

    ```html
    <h1><%= @blog.title %></h1>
    <p><%= @blog.body %></p>
    ```

    <img src='assets/active-admin-15.png'/>

    Ten en cuenta que puedes agregar el archivo con extensión `.arb` en vez de `.erb` y usar la gema [Arbre](https://activeadmin.github.io/arbre/) que es el DSL que Active Admin utiliza para dibujar sus vistas. El siguiente código:

    `/app/views/admin/blogs/preview.html.arb`

    ```ruby
    h1 { resource.title }
    para { resource.body }
    ```

    sería equivalente a lo de `/app/views/admin/blogs/preview.html.erb` en Arbre.

### Link de download para archivos

> Para manejo de archivos se supone el uso de Shrine

Suponiendo que el blog tiene un archivo `image`:

1. Agregar la `member_action`:

    ```ruby
    member_action :download, method: :get do
      blog = Blog.find(params[:id])
      send_file blog.image.download
    end
    ```

1. Agregar el `action_item`:

    ```ruby
    action_item :download, only: :show, if: -> { blog.image.present? } do
      link_to 'Download', download_admin_blog_path(blog)
    end
    ```

1. Agregar link a las `actions` en el index

    ```ruby
    index do
      column :id
      .
      .
      .
      actions defaults: true do |blog|
        link_to 'Download Image', download_admin_blog_path(blog)
      end
    end
    ```

### JavaScript en una vista

Si necesitas agregar alguna pequeña inteligencia en una vista de Active Admin revisa nuestra guía sobre [AlpineJS](https://www.notion.so/js/alpine/README.md).

### Active Admin Addons

Es una [gema](https://github.com/platanus/activeadmin_addons) construida en Platanus sobre Active Admin y la utilizamos para facilitar algunas features comunes. Por ejemplo: [inputs con select2](https://github.com/platanus/activeadmin_addons/blob/master/docs/select2_default.md), [selector de booleanos en index/show](https://github.com/platanus/activeadmin_addons/blob/master/docs/toggle_bool.md), [selector de colores](https://github.com/platanus/activeadmin_addons/blob/master/docs/color-picker.md), etc.

### Inputs custom

Muchas veces en un formulario de Active Admin necesitamos un input custom. Por ejemplo: selectores de moneda, de colores, con búsqueda ajax, etc. Por esto, Active Admin ofrece una forma sencilla de agregar estos controles. Para mostrarte cómo funciona te mostraré en pasos como agregar un input que escribe con `console.info` lo que se escribió en input en el evento focus out.

1. Agregar el archivo con mi input en `/app/inputs/logger_input.rb`.

    > Ten en cuenta que el nombre del archivo debe tener la forma: [nombre_control]_input y lo mismo para el nombre de la clase, pero en CamelCase.

    ```plain text
    class LoggerInput < ActiveAdminAddons::InputBase
      def render_custom_input
        concat(label_html)
        concat(builder.text_field(method, input_html_options))
      end
    end
    ```

1. Agregar el js con la lógica del focus out en `/app/assets/javascripts/admin/inputs/logger_input.js`

    ```plain text
    $(document).ready(function(){
      $('.logger-input').each(function(i, el) {
        $(el).on( "focusout", function(event) {
          console.info(event.currentTarget.value);
        });
      });
    });
    ```

1. Incluir el js en `/app/assets/javascripts/active_admin.js`:

    ```plain text
    //= require active_admin/base
    //= require admin/inputs/logger_input
    ```

1. Usar en el form:

    ```plain text
    ActiveAdmin.register Blog do
      form do |f|
        f.inputs do
          f.input :title, as: :logger
        end
    
        f.actions
      end
    end
    ```

### Filtros custom

Para manejar la búsqueda de los filtros ActiveAdmin por debajo usa [Ransack](https://github.com/activerecord-hackery/ransack). Esta gema usa [distintos sufijos](https://github.com/activerecord-hackery/ransack#search-matchers) para indicar distintos tipos de búsqueda. Además, nos permite hacer búsquedas con respecto a [atributos de asociaciones](https://github.com/activerecord-hackery/ransack#associations).

Por ejemplo, si queremos buscar blogs por nombre de usuario:

```ruby
filter :user_name_cont
```

Aquí `_cont` indica que se entregarán los resultados que contengan el valor dado. Podríamos haber usado `_eq` si quisieramos un match perfecto, por ejemplo.

### Utilizando Vue

Muchas veces lo que se puede hacer con AA es un poco riguroso en cuanto a las necesidades del cliente, por lo que pueden haber situaciones en las cuales necesitemos incorporar Vue.js en AA. A continuación se explica una guía paso a paso de cómo agregar este en AA.

Para esto tenemos 2 opciones:

1. Hacer una vista custom como se mencionó anteriormente y ahí usar Vue.

1. Usar directamente un componente en la vista de admin o en un vista html.arb.

### Primer caso.

1. Se crea el componente vue que queremos utilizar, por ejemplo `massive-edit.vue`.

1. Luego debemos registrar el componente globalmente. Acá necesitamos usar un archivo diferente al `application.js` que utilizamos normalmente, para este caso usaremos un archivo llamado `admin_application.js` que se debe encontrar en la misma carpeta que el `application.js`, si no se encuentra debe crearlo, la convención para registrar los componentes que se usan en admin es de `snake_case`, siguiendo con el ejemplo anterior el archivo se vería así:

    ```javascript
    import Vue from 'vue/dist/vue.esm';
    import MassiveEdit from '../views/products/massive-edit.vue';
    
    Vue.component('massive_edit', MassiveEdit);
    
    document.addEventListener('DOMContentLoaded', () => {
      if (document.getElementById('wrapper') !== null) {
        return new Vue({
          el: '#wrapper',
        });
      }
    
      return null;
    });
    ```

1. Si queremos utilizar filtros, i18n o tailwind en los archivos Vue que utilizaremos en AA, debemos importarlos en este archivo también, tal como lo hacemos normalmente. Cabe destacar que si tenemos algo importado en `application.js` no va a funcionar en los componentes que se utilicen en AA, si no que debemos importarlos nuevamente en `admin_application.js`, acá un ejemplo del archivo completo:

    ```javascript
    import { camelizeKeys } from 'humps'; // filtro camelize
    import Vue from 'vue/dist/vue.esm';
    import MassiveEdit from '../views/products/massive-edit.vue';
    import i18n from '../plugins/i18n'; // i18n
    import '../css/application.css'; // tailwind
    
    Vue.component('massive_edit', MassiveEdit);
    Vue.filter('camelizeKeys', camelizeKeys);
    
    document.addEventListener('DOMContentLoaded', () => {
      if (document.getElementById('wrapper') !== null) {
        return new Vue({
          el: '#wrapper',
          i18n,
        });
      }
    
      return null;
    });
    ```

1. Luego podemos ir a cualquier vista dentro de `app/views/admin` y usar el componente como lo hacemos normalmente:

    ```html
     <massive_edit :props="@props.to_json"> </massive_edit>
    ```

### Segundo caso.

1. Hacer los mismos pasos anteriores.

1. Instalar la clase `vue_component` desde nuestra gema potassium. Esto agrega un par de inicializadores a nuestra app. Se debe escribir `potassium install` y escoger `vue_component`.

1. Luego en el archivo `initializers/active_admin.rb` debemos importar `vue_componment` y hacer build del componente registrado anteriormente:

    ```plain text
        require "vue_component.rb"
    
        AUTO_BUILD_ELEMENTS = %i{
          massive_edit
        }
    
        component_creator(AUTO_BUILD_ELEMENTS)
    ```

    La función component_creator recibe los nombres de los componentes y los convierte a html que puede procesar AA mediante la gema [Arbre](https://github.com/activeadmin/arbre). Lo anterior se debe poner fuera del `ActiveAdmin.setup`.

1. Utilizar el componente en la vista, puede ser de las siguientes formas:

    a. html.arb:

    ```ruby
    massive_edit(prop1: prop1, prop2: prop2)
    ```

    b. admin

    ```ruby
    index do
      massive_edit(prop1: prop1, prop2: prop2)
    end
    ```

## Recursos útiles

* [Active Admin Docs](https://activeadmin.info/documentation.html)

* [Active Admin Addons Docs](https://github.com/platanus/activeadmin_addons)
