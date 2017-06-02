# Rails Deployment

Los projectos rails se publican en [heroku][heroku-dashboard] en la cuenta de Platanus.

Utilizaremos los [pipelines][heroku-pipelines] de heroku para manejar diferente stages de la aplicación. Como convencion partiremos siempre con *staging* y *production*.

## Creación de la app en heroku

Al crear un proyecto rails con potassium, si tienes acceso a la cuenta de heroku, las aplicaciones seran creadas por el mismo commando `create` de potassium.
[Como generar una aplicacion rails?](../code/rails.md)

> Para crear la aplicación se deben usar la cuenta *owner* que es tiene permisos para crear nuevas aplicaciones. Para esto debes installar el [heroku-toolbelt][] y la gema [potassium][].

Debes hacer login con la cuenta de heroku

    heroku login

y crear la aplicación

    potassium create <app-name>

Esto creará una aplicacion para cada **stage**, creará el **pipeline** y hará la asociación entre las apps y el stage. Todo esto esta definido en la [receta heroku][heroku-recipe] de potassium.

Si tienes un proyecto que todavia no tiene sus aplicaciones creadas en heroku, puedes ejecutar nuevamente la receta con el comando `install`

    potassium install heroku

#### Conectar github

Entrar al [dashboard de heroku][heroku-dashboard] y conectar el pipeline con un repositorio en github y configurar los **automatic deploys** usando github.

![Hint](https://cloud.githubusercontent.com/assets/313750/13019759/fa86c8ca-d1af-11e5-8869-cd2efb5513fa.png)

Al configurar los **automatic deploys** hay que elegir un branch para cada stage.

| branch     | stage      |
| ---------- | ---------- |
| master     | staging    |
| production | production |

> NOTA: Si vas a habilitar CI, al conectar un branch a un stage debes habilitar
la opcion que dice, esperar CI antes de publicar. Eso para que solo se publiquen
braches en los que los tests estan pasando.

##### Continuous integration

Los test de la aplicación ejecutados por el servicio CircleCi. Para esto debes habilitar el repositorio en https://circleci.com/add-projects.

##### Continuous delivery

El deploy se hace de manera automática mediante usando los branches definidos para cada stage.

Cada vez que se hace un push al repositorio en github a uno de estos branches, la aplicación del stage correspondiente comienza su proceso de build y luego es publicada.

## Usando heroku desde la linea de commando

Para comenzar a usar heroku desde la linea de comando debes instalar [heroku-toolbelt][]

    brew install heroku-toolbelt

Luego debes hacer login con tu cuenta de heroku.

    heroku login

Para acceder mas facil a las aplicaciones en heroku desde tu proyecto, el heroku toolbelt usa los remotes de github para saber en que stage o aplicación ejecutar un comando.

Potassium crea los remotes automaticamente a generar la aplicacion. Si acabas de clonar una aplicación existente puedes ejecutar el script `bin/setup`.

Luego de esto puedes ejecutar los comandos de la siguiente manera

    heroku logs --remote staging
    heroku config:set KEY=value --remote production

> El remote staging queda configurado como por defecto, así que puedes omitirlo.

Antes de poder hacer un deploy se debe configurar en Heroku es la base de datos, o DATABASE_URL, si el proyecto usa MySQL, sólo basta con añadirla así:

    heroku config:set DATABASE_URL=MY_URL --remote staging

Si en staging preferimos usar el addon de Heroku para Postgres, primero hay que cambiar la configuración de la base de datos a esto:

```ruby
# config/database.yml
staging:
  adapter: postgresql
  encoding: unicode
  url: <%= ENV.fetch("DATABASE_URL", "") %>
  pool: <%= [Integer(ENV.fetch("MAX_THREADS", 5)), Integer(ENV.fetch("DB_POOL", 5))].max %>
```

Y luego crear la base de datos mediante la línea de comandos:

    heroku addons:create heroku-postgresql:hobby-dev -a nombre-de-nuestra-aplicacion

Esto genera un URL en la configuración ambiental del proyecto que podemos ver con `heroku config`

    HEROKU_POSTGRESQL_CHARCOAL_URL: URL_DE_LA_DB_QUE_CREO_HEROKU

Debemos asignar el valor de esta URL hacia DATABASE_URL para que el deploy funcione:

    heroku config:set DATABASE_URL=URL_DE_LA_DB_QUE_CREO_HEROKU --remote staging

Una vez hecho esto, ya podemos hacer deploys mediante un push del branch que tenemos configurado en Heroku (por defecto es master):

    git push heroku master

[heroku-dashboard]: https://dashboard.heroku.com
[heroku-pipelines]: https://devcenter.heroku.com/articles/pipelines
[heroku-toolbelt]: https://toolbelt.heroku.com/
[heroku-recipe]: https://github.com/platanus/potassium/blob/master/lib/potassium/recipes/heroku.rb
[potassium]: https://github.com/platanus/potassium
