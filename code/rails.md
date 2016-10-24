# Rails

## Overview

* Inicializamos nuestros proyectos con [potassium][]
* El deploy se hace a [heroku][heroku-dashboard] usando continuous delivery. Más información en la sección [deployment de rails](../deployment/rails)
* Cuando nuestros proyectos incluyen una API Rails, utilizamos la gema
[simple_token_authentication][]
para manejar la autenticación. Más información en este [blog post][simple-token-blog]
* Las interfaces de administración las contruimos usando [Active Admin](activeadmin.md)
* Para resolver el problema de autenticar usuarios que no pueden registrarse
libremente en una aplicación utilizamos [devise_invitable][]
* Para manejar "Enum attributes" utilizamos la gema [enumerize][]
* Como estrategia para el manejo de attachments en nuestras APIs, usamos la gema [rails_pallet][]

## Getting started

### Proyecto nuevo

Para empezar un projecto nuevo, asegurate de tener instalada la última version
de [potassium][]

    gem install potassium


Luego crea un nuevo projecto con potassium contestando las preguntas y finalmente corre el script de setup:

    potassium create <project-name>

### Proyecto existente

Si vas a empezar a trabajar en un projecto Rails que alguien ya creó.

    git clone platanus/<project-name>
    cd <project-name>
    bin/setup

Si necesita agregar funcionalidad a un projecto existente puedes usar
el command `install <recipe>` de potassium

    cd <project-name>
    potassium install pudnit

[heroku-dashboard]: https://dashboard.heroku.com
[potassium]: https://github.com/platanus/potassium
[simple-token-blog]: https://cb.platan.us/usando-angular-auth-lib-con-simple-token-authentication-gem
[devise_invitable]: http://cb.platan.us/rails/active%20admin/devise/2015/03/18/invitar-usuarios-con-devise.html
[simple_token_authentication]: https://github.com/gonzalo-bulnes/simple_token_authentication
[rails_pallet]: https://github.com/platanus/rails_pallet
[enumerize]: https://github.com/brainspec/enumerize
