Rails
=====

* Inicializamos nuestros proyectos con [potassium][]
* El deploy se hace a [heroku][heroku-dashboard] usando continuous delivery. Más información en la sección [deploy](#Deployment)
* Cuando nuestros proyectos incluyen una API Rails, utilizamos la gema
[simple_token_authentication](https://github.com/gonzalo-bulnes/simple_token_authentication)
para manejar la autenticación. Más información en este [blog post](https://cb.platan.us/usando-angular-auth-lib-con-simple-token-authentication-gem)
* Las interfaces de administración las contruimos usando [Active Admin](/code/activeadmin.md)
* Para resolver el problema de autenticar usuarios que no pueden registrarse libremente en una aplicación utilizamos [devise_invitable](http://cb.platan.us/rails/active%20admin/devise/2015/03/18/invitar-usuarios-con-devise.html)
* Para manejar "Enum attributes" utilizamos la gema [enumerize](https://github.com/brainspec/enumerize)
* Como estrategia para el manejo de attachments en nuestras APIs, usamos la gema [rails_pallet](https://github.com/platanus/rails_pallet)

[blog post]: https://cb.platan.us/usando-angular-auth-lib-con-simple-token-authentication-gem

## Getting started

### Proyecto existente

Si vas a empezar a trabajar en un projecto Rails que alguien ya creó.

```shell
git clone platanus/<project-name>
cd <project-name>
./bin/setup
```

### Proyecto nuevo

Para empezar un projecto nuevo, asegurate de tener instalada la última version de [potassium][potassium]

```shell
gem install potassium
```

Luego crear un nuevo projecto con potassium contestando las preguntas.

```shell
potassium create <project-name>
cd <project-name>
./bin/setup
```

## Deployment

Los projectos rails se publican en [heroku][heroku-dashboard] en la cuenta de Platanus. La aplicación debe ser creada esta cuenta antes de hacer un deploy.

Utilizaremos los [pipelines][heroku-pipelines] de heroku para manejar diferente stages de la aplicación. Como convencion partiremos siempre con *staging* y *production*.

#### Creación de la app en heroku

Para crear la aplicación se deben usar la cuenta *owner* que es tiene permisos para crear nuevas aplicaciones. Para esto debes installar el [heroku-toolbelt][] y la gema [potassium][].

Debes hacer login con la cuenta de heroku

```shell
heroku login
```

 y crear la aplicación

``` shell
potassium heroku create
```

Esto creará una aplicacion para cada **stage**, creará el **pipeline** y hará la asociación entre las apps y el stage.

##### Connect github y continuous delivery

Entrar al [dashboard de heroku][heroku-dashboard] y conectar el pipeline con un repositorio en github y configurar los **automatic deploys** usando github.

![Hint](https://cloud.githubusercontent.com/assets/313750/13019759/fa86c8ca-d1af-11e5-8869-cd2efb5513fa.png)

Al configurar los **automatic deploys** hay que elegir un branch para cada stage.

| branch     | stage      |
| ---------- | ---------- |
| master     | staging    |
| production | production |

#### Continuous delivery

El deploy se hace de manera automática mediante usando los branches definidos para cada stge.

Cada vez que se hace un push al repositorio en github a uno de estos branches, la aplicación del stage correspondiente comienza su proceso de build y luego es publicada.

{% getPostsByTag 'rails', 'ruby' %}{% endgetPostsByTag %}

[heroku-dashboard]: https://dashboard.heroku.com
[heroku-pipelines]: https://devcenter.heroku.com/articles/pipelines
[heroku-toolbelt]: https://toolbelt.heroku.com/
[potassium]: https://github.com/platanus/potassium
