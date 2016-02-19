Rails
=====

* Inicializamos nuestros proyectos usando [potassium][]
* El deploy lo hacemos a [heroku][heroku-dashboard] usando continuous delivery. Hay más información sobre esto en la sección [deploy](#Deployment)
* Cuando nuestros proyectos incluyen una API, utilizamos la gema
[simple_token_authentication](https://github.com/gonzalo-bulnes/simple_token_authentication)
para manejar la autenticación. Más información en este [blog post](https://cb.platan.us/usando-angular-auth-lib-con-simple-token-authentication-gem)
* Las interfaces de administración las construimos usando [Active Admin](/code/activeadmin.md)
* Para resolver el problema de autenticar usuarios que no pueden registrarse libremente en una aplicación utilizamos [devise_invitable](http://cb.platan.us/rails/active%20admin/devise/2015/03/18/invitar-usuarios-con-devise.html)
* Para manejar "Enum attributes" utilizamos la gema [enumerize](https://github.com/brainspec/enumerize)
* Como estrategia para el manejo de attachments en nuestras APIs, usamos la gema [rails_pallet](https://github.com/platanus/rails_pallet)

[blog post]: https://cb.platan.us/usando-angular-auth-lib-con-simple-token-authentication-gem

## Getting started

### Proyecto existente

Si vas a empezar a trabajar en un projecto Rails que alguien ya creó, clona el proyecto y corre el script de setup:

```shell
git clone platanus/<project-name>
cd <project-name>
./bin/setup
```

### Proyecto nuevo

Para empezar un proyecto nuevo, asegurate de tener instalada la última version de [potassium][potassium]

```shell
gem install potassium
```

Luego crea un nuevo projecto con potassium contestando las preguntas y finalmente corre el script de setup:

```shell
potassium create <project-name>
cd <project-name>
./bin/setup
```

## Deployment

Los projectos Rails se publican en [heroku][heroku-dashboard] en la cuenta de Platanus. La aplicación debe ser creada en esta cuenta antes de hacer un deploy.

Utilizaremos los [pipelines][heroku-pipelines] de Heroku para manejar diferentes stages de la aplicación. Como convención, partiremos siempre con *staging* y *production*.

#### Creación de la app en Heroku

Para crear la aplicación se debe usar la cuenta *owner* que tiene permisos para crear nuevas aplicaciones. Para esto debes instalar el [heroku-toolbelt][] y la gema [potassium][].

Debes hacer login con la cuenta de Heroku

```shell
heroku login
```

 y crear la aplicación

``` shell
potassium heroku create
```

Esto creará una aplicacion para cada **stage**, creará el **pipeline** y hará la asociación entre las apps y el stage.

##### Connect github

Entra al [dashboard de Heroku][heroku-dashboard] y conecta el pipeline con un repositorio en Github y configura los **automatic deploys** usando Github.

![Hint](https://cloud.githubusercontent.com/assets/313750/13019759/fa86c8ca-d1af-11e5-8869-cd2efb5513fa.png)

Al configurar los **automatic deploys** hay que elegir un branch para cada stage.

| branch     | stage      |
| ---------- | ---------- |
| master     | staging    |
| production | production |

##### Continuous delivery

Cada vez que haces un push al repositorio en Github a uno de estos branches, la aplicación del stage correspondiente comienza su proceso de build y luego es publicada.

{% getPostsByTag 'rails', 'ruby' %}{% endgetPostsByTag %}

[heroku-dashboard]: https://dashboard.heroku.com
[heroku-pipelines]: https://devcenter.heroku.com/articles/pipelines
[heroku-toolbelt]: https://toolbelt.heroku.com/
[potassium]: https://github.com/platanus/potassium
