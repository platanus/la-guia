# Rails

## Rails Deployment

Los proyectos Rails se publican en [heroku](https://dashboard.heroku.com/) en la cuenta de Platanus.

Utilizaremos los [pipelines](https://devcenter.heroku.com/articles/pipelines) de heroku para manejar diferente stages de la aplicación. Como convención partiremos siempre con *staging* y *production*.

### Creación de la app en heroku

Al crear un proyecto rails con potassium, si tienes acceso a la cuenta de heroku,
las aplicaciones serán creadas por el mismo comando `create` de potassium.

> Para crear la aplicación se deben usar la cuenta owner que es tiene permisos para crear nuevas aplicaciones. Para esto debes installar el heroku-toolbelt y la gema potassium.

Debes hacer login con la cuenta de heroku

```bash
heroku login
```

y crear la aplicación

```bash
potassium create <app-name>
```

Esto creará una aplicacion para cada **stage**, creará el **pipeline** y hará la asociación entre las apps y el stage. Todo esto esta definido en la [receta heroku](https://github.com/platanus/potassium/blob/master/lib/potassium/recipes/heroku.rb) de potassium.

Si tienes un proyecto que todavia no tiene sus aplicaciones creadas en heroku,
puedes ejecutar nuevamente la receta con el comando `install`

```bash
potassium install heroku
```

### Conectar github

Entrar al [dashboard de heroku](https://dashboard.heroku.com/) y conectar el pipeline con un repositorio en github y configurar los **automatic deploys** usando github.

<img src='assets/rails-1.png'/>

Al configurar los **automatic deploys** hay que elegir un branch para cada stage.

> NOTA: Si vas a habilitar CI, al conectar un branch a un stage debes habilitar
la opcion que dice, esperar CI antes de publicar. Eso para que solo se publiquen
braches en los que los tests estan pasando.

### Continuous integration

Los test de la aplicación ejecutados por el servicio CircleCi. Para esto debes
habilitar el repositorio en [https://circleci.com/add-projects](https://circleci.com/add-projects).

### Continuous delivery

El deploy se hace de manera automática mediante usando los branches definidos para cada stage.

Cada vez que se hace un push al repositorio en github a uno de estos branches, la aplicación del stage correspondiente comienza su proceso de build y luego es publicada.

### Usando heroku desde la linea de comando

Para comenzar a usar heroku desde la linea de comando debes instalar [heroku-toolbelt](https://toolbelt.heroku.com/)

```bash
brew install heroku-toolbelt
```

Luego debes hacer login con tu cuenta de heroku.

```bash
heroku login
```

Para acceder mas fácil a las aplicaciones en heroku desde tu proyecto, el heroku
toolbelt usa los remotes de github para saber en que stage o aplicación ejecutar
un comando.

Potassium crea los remotes automáticamente a generar la aplicacion. Si acabas de
clonar una aplicación existente puedes ejecutar el script `bin/setup`.

Luego de esto puedes ejecutar los comandos de la siguiente manera

```bash
heroku logs --remote staging
heroku config:set KEY=value --remote production
```

> El remote staging queda configurado como por defecto, por lo que puedes omitirlo.

**branch → stage**

master → staging

production → production


