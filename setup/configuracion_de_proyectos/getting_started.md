# Getting Started

> Soy nuev@ y ya tengo asignado mi primer pitch! Qué tengo que hacer para levantar el proyecto y tener todo listo para ponerme a programar?

Esto es lo que queremos responder con esta sección:

1. Asegúrate de haber revisado la sección de configuración local y que tengas andando tu ambiente con todo instalado (node, ruby, docker, plugins de tu editor, etc).

1. Clona el repositorio y muevete a la nueva carpeta:

    ```bash
    git clone <https://github.com/platanus/><project-name>.git
    cd project-name
    ```

1. Corre `bin/setup`. Esto te deja instaladas las gemas y paquetes que necesite el paquete, además de correr el setup de la base de datos. Puedes revisar el archivo para ver qué exactamente se está corriendo

    * A veces puede salir el error `Error: remote staging not found in git remotes` después de correr `bin/setup`. Si te aparece, probablemente es porque no tienes acceso al heroku del proyecto. Pídele acceso al encargado del proyecto y cuando lo tengas corre `bin/setup_heroku` para reintentar el paso que falló

1. Para no empezar de 0 con una base de datos vacía, los proyectos tienen un `Makefile` con un par de comandos útiles para traernos datos de staging:

    * Puedes correr `make backup-staging` para generar un nuevo backup en la base de datos de staging. Esto nos asegura que tengamos los datos más actualizados de staging

    * `make restore-from-staging` toma el último backup de staging y lo copia en tu base de datos local

1. Si todo salió bien, con esto deberías estar listo para correr el proyecto. Corre los siguientes comandos en paralelo en pestañas separadas:

    * `bin/rails s`: levanta el servidor. Si vas a `localhost:3000` en el navegador verías la página

    * `bin/webpack-dev-server` o `bin/webpacker-dev-server` en proyectos más nuevos: permite que cada vez que se guarde un archivo js/vue, se recargue la página automáticamente

    * `bundle exec guard`: cada vez que guardas un archivo ruby se ejecutan los tests correspondientes a ese archivo. Alternativamente, puedes correr todos los tests de manera manual usando `bin/rspec`

        * Si quieres correr un `it`, `context` o `describe` en particular, ignorando otros archivos y los demás ejemplos en el mismo archivo, puedes agregar una `f` al comienzo de este. Esto bunciona tanto para `guard` como para `rspec`. Esto es solo posible gracias a [filter_run_when_matching](https://relishapp.com/rspec/rspec-core/v/3-6/docs/filtering/filter-run-when-matching), por lo que debe estar configurado en el proyecto para poder usarlo

    * `bin/rails c`: abre la consola de rails. En ella puedes probar cosas, por ejemplo, buscar o crear records. Puedes correr cualquier código Ruby/Rails, llamar a modelos/jobs/clients definidos en el proyecto, etc. No es estrictamente necesario, pero puede ser muy útil

{% hint style="info" %}
Es posible que haciendo el setup te encuentres con un error como el siguiente:

> gyp verb 'which' failed Error: not found: python2

Si te ocurre, puede que tengas `yarn` instalado con `brew`, y en ese caso no se le puede indicar que use `python2`. Para solucionarlo, puedes instalar yarn a través de `npm` corriendo lo siguiente:

```bash
npm config set python /usr/bin/python
brew uninstall yarn
npm install -g yarn
nodenv rehash
```

{% endhint %}

Ahora, cada vez que quieras levantar o volver a trabajar en el proyecto, puede que tengas que hacer alguna de estas cosas:

1. Debes asegurarte de tener la DB corriendo. En Platanus tenemos las bases de datos de nuestros proyectos dockeridas. Esto quiere decir que no corre en el `postgres` que tengas directamente instalado en tu computador, si no que corre en un postgres que está dentro de un container de docker. Para correr el container debes usar el comando `docker-compose up -d`

    * Este paso no lo tuviste que hacer en el setup inicial explícitamente ya que está incluído dentro de las cosas que hace el `bin/setup`

    * Para ver cuáles containers están prendidos, puedes correr `docker container ls`. Si quieres una alternativa más "visual" puedes usar [Captain](https://getcaptain.co/) en OSX

1. Si alguien agregó cambios nuevos a master, es bueno traerlos frecuentemente a tu rama, así se resuelven periódicamente los conflictos que puedan aparecer. Para esto, usa rebase. Corre **en tu rama** `git pull origin master` para traerte los últimos cambios, y luego `git rebase -i master`. Esto te mostrará los commits que has agregado en tu rama y que quedarían sobre los de master. Si se encuentra un conflicto, el rebase para en el commit que lo contiene y te deja corregirlo antes de indicarle que siga

1. Si alguien más está trabajando en el proyecto, puede que se hayan agregado nuevas gemas o paquetes. Para eso tendrías que correr `bundle install` y/o `yarn install`

1. Si alguien más está trabajando en el proyecto, puede que se hayan agregado nuevas migraciones. Para eso tendrías que correr `bundle exec rails db:migrate:with_data`. Si quieres saber por qué el `with_data` puedes ver la sección de: 

    [Data Migrate](stack/ruby_rails/data_migrate.md)

1. Si hiciste un PR y te pidieron cambios, por lo general aplicamos esos cambios usando rebase. [En este post de nuestro blog](https://plata.news/blog/manteniendo-la-historia-limpia-usando-git-rebase/) tenemos más info sobre rebase y cómo lo usamos para mantener la historia limpia. También revisa [este post](https://fle.github.io/git-tip-keep-your-branch-clean-with-fixup-and-autosquash.html) sobre fixup, otra herramienta del rebase que usamos para esto

1. Si los puntos anteriores no aplican y solo quieres volver a levantar el proyecto, basta con que repitas el paso 5 anterior (`bin/rails s`, `bin/webpack-dev-server` y `bundle exec guard`), no debes correr el `bin/setup` de nuevo ni nada


