## [Sidekiq](https://github.com/mperham/sidekiq)

Es la gema que usamos para correr tareas de manera asíncrona.

### ¿Por qué la usamos?

Imagínate que cuando un usuario se registra en la aplicación, debemos subir su foto a un storage, y luego mandarle un correo de bienvenida. Estas tareas pueden ser demandantes de tiempo, y además que pueden ser ejecutadas "*in background*". Así evitamos tener al usuario esperando. Estos procesos suelen correr en un `Job` que corre de manera asíncrona. En Platanus usamos Sidekiq para este tipo de tareas.

### ¿Cómo la usamos?

#### Instalación
La gema viene instalada si el proyecto se generó usando [Potassium](https://github.com/platanus/potassium). Si no es así, igual puedes agregarlo luego ejecutando `potassium install background_processor`.

Existen dos formas de usar esta gema. La primera es usar la implementación propia de Sidekiq. La segunda, es usar el adaptador de Rails para este tipo de tareas que se llama [`ActiveJobs`](https://guides.rubyonrails.org/active_job_basics.html). Para esta guía nos basaremos en esta última.

La idea de este adaptador de Rails, es que podemos usar una interfaz común entre los distintos tipos de *background processors* que existen. La magia de esto ocurre en `config/application.rb`:

```ruby
class Application < Rails::Application
  # ...
  config.active_job.queue_adapter = :sidekiq
end
```

#### Ambiente de desarrollo

Probablemente no necesites todo el poder de jobs asíncronos a la hora de desarrollar. Este caso puedes definir que use el `adapter` que usa Rails por default para desarrollar, [`AsyncAdapter`](https://api.rubyonrails.org/classes/ActiveJob/QueueAdapters/AsyncAdapter.html):

```ruby
# config/enviroments/development.rb
class Application < Rails::Application
  # ...
  config.active_job.queue_adapter = :async
end
```

Con esta configuración no tenemos la necesidad de levantar el proceso de Sidekiq, tema explicado más adelante.

#### Configuración

El instalador de la gema crea el archivo `config/sidekiq.yml`. En este podemos configurar todo lo relacionado a Sidekiq.

Dentro podemos configurar:
- **Concurrency**: define cuantos threads se van a usar. Además podemos configurar distintas opciones según el ambiente a usar.
- **Queues**: define las distintas colas a usar. Más adelante vamos a explicar como. Por ahora solo ten en cuenta que se definen por orden de prioridad.

Un ejemplo es el siguiente:

```yml
staging:
  :concurrency: 10
production:
  :concurrency: 20
:queues:
  - critical
  - default
  - low
```

#### Iniciar Sidekiq

Sidekiq corre en un proceso aparte que nuestra aplicación Rails. Estos se comunican usando Redis. El último funciona como base de datos para los distintos procesos. Vamos a suponer que ya tienes un cliente de Redis configurado y corriendo.

 Para empezar a correr `Jobs`, basta con correr en otra pestaña de nuestro terminal lo siguiente:

```
bundle exec sidekiq
```

#### Uso básico

Una vez configurado lo anterior, podemos crear un `Job` encargado de mandar un mails. `rails generate job SendWelcomeEmail`, y completamos el archivo recién creado con la implementación necesaria:

```ruby
class SendWelcomeEmailJob < ActiveJob::Base
  queue_as :default

  def perform(user)
    SendWelcomeEmailCommand.for(user: user)
  end
end
```

En Platanus tenemos la práctica de no definir mucha lógica de negocio dentro de los `Jobs`. Para eso mejor creamos un [Command](https://la-guia.platan.us/stack/ruby/rails/powertypes#commands-app-commands) que se encargue de procesar lo necesario, y así poder testearlo de manera unitaria.

#### Formas de encolar un Job

Buenísimo, tenemos nuestro `Job`. Podemos usarlo de distintas formas:

- **Ejecutar lo antes posible:** en cuanto la cola definida se libere se ejecutará el job.
  ```ruby
  SendWelcomeEmailJob.perform_later(user)
  ```
- **Ejecutar en un momento dado:** se ejecutará el job después del tiempo definido.
  ```ruby
  SendWelcomeEmailJob.set(wait_until: Date.tomorrow.noon).perform_later(user)
  ```
- **Ejecutar pasado cierto tiempo:** se ejecutará después del plazo dado.
  ```ruby
  SendWelcomeEmailJob.set(wait: 1.week).perform_later(user)
  ```
- **Ejecutar inmediatamente:** corre el job de manera inmediata, bloqueando la ejecución de tu aplicación. Ojo, esto no llega a Sidekiq, se ejecuta antes.
  ```ruby
  SendWelcomeEmailJob.perform_now(user)
  ```
  Esta última alternativa sería equivalente a correr un `Command` directamente. En Platanus preferimos usar el `Commmand` en vez de un Job que se llame así.

#### Configuraciones de un Job

En la sección de configuración definimos distintas *queues*. En caso que tengamos distintas prioridades para ciertos procesos, podemos especificar la cola a usar usando la opción `queue_as`.

También podemos especificar la política en caso de que no se logre ejecutar de manera correcta el job. Esto lo hacemos especificando la opción `retry`. ActiveJob por defecto tiene una política de **reintentar** 5 veces, cada una separada por 3 segundos. Luego de esto se usa la implementación por defecto de Sidekiq, en el que se vuelven a encolar estos jobs pero con un delay exponencial.

Además podemos especificar que el `Job` se **descarte** en caso de una excepción en específico.

A modo de ejemplo, un proceso que use las dos opciones descritas puede ser definido de la siguiente manera:

```ruby
class ReallyImportantJob < ActiveJob::Base
  queue_as :urgent
  discard_on CustomAppException

  def perform(*args)
    # ...
  end
end
```

- En el ejemplo anterior usamos el adaptador de ActiveJobs para añadir estas opciones extras. Puedes revisar con más detalle [aquí](https://edgeguides.rubyonrails.org/active_job_basics.html#retrying-or-discarding-failed-jobs).

- Existen opciones más avanzadas específicas de Sidekiq de que puedes revisar en la [documentación](https://github.com/mperham/sidekiq/wiki/Advanced-Options). OJO 👀! Esto está disponible desde Rails 6.0.1.

#### Ciclo de vida de un Job

Un `Job` puede encontrarse en los siguientes estados: `processed`, `failed` o `dead`. Este último es un estado final cuando ya se le agotan las vidas disponibles que tenía para hacer `retry` 💀.

#### Panel de Sidekiq

La gema ofrece una vista donde se puede ver y editar el estado de los jobs en la aplicación. Si vas a `config/routes.rb`, vas a ver algo del estilo `mount Sidekiq::Web => '/queue'`. Esto indica que la vista puede ser accedida desde `http://localhost:3000/queue`.

<img src="./assets/sidekiq-panel.png" />

#### Trabajos recurrentes

Ponte en el caso en que quisieras mandar un correo a los usuarios todos los días a las 8:00hrs con un chiste para acompañar su café. Para esto podemos usar la gema `sidekiq-scheduler`. Esta también viene en la configuración de Potassium. Pero también puedes agregarla ejecutando `potassium install schedule`.

Supongamos que tenemos nuestro Job definido:

```ruby
class SendUsersAJokeJob < ApplicationJob
  queue_as :default

  def perform
    # Get a funny meme and send it to all users.
  end
end
```

Podemos definir la recurrencia de este Job usando un [Cron](https://crontab.guru/). Estos se definen en `config/sidekiq.yml`

```yml
:schedule:
  SendUsersAJokeJob:
    cron: '0 8 * * * *' # Runs all days at 8:00 hrs.
    class: HelloWorld
```

### Recursos útiles

- [Sidekiq Docs](https://github.com/mperham/sidekiq/wiki/Getting-Started)
- [Sidekiq + ActiveJob](https://github.com/mperham/sidekiq/wiki/Active+Job)
- [Sidekiq-history](https://github.com/russ/sidekiq-history)
- [ActiveJob](https://edgeguides.rubyonrails.org/active_job_basics.html)
- [Buenas prácticas](https://github.com/mperham/sidekiq/wiki/Best-Practices)

### Sidekiq Pro

Existe una versión pagada de la gema. Esta ofrece algunas funcionalidades más avanzadas. Para la gran mayoría de los casos de usos, seguro la versión gratis ofrece todo lo necesario.