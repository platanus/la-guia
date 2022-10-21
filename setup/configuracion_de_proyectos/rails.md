# Rails

## Crear un proyecto nuevo

Para empezar un projecto nuevo, asegurate de tener instalada la última version
de [potassium](https://github.com/platanus/potassium)

```bash
gem install potassium
```

Luego crea un nuevo proyecto con potassium contestando las preguntas y finalmente corre el script de setup:

```bash
potassium create <project-name>
```

### Proyecto existente

Si vas a empezar a trabajar en un proyecto Rails que alguien ya creó.

```bash
git clone platanus/<project-name>
cd <project-name>
bin/setup
```

Si necesita agregar funcionalidad a un proyecto existente puedes usar
el command `install <recipe>` de potassium

```bash
cd <project-name>
potassium install pundit
```

### Manejo de variables de entorno

Los proyectos siguen el patrón [12 factor](http://12factor.net/config) para configurar la aplicación en diferentes ambientes sin tener que cambiar el código.

En el caso de nuestro ambiente de **desarrollo y testing** no queremos tener keys de diferentes
proyectos en nuestro ambiente, es por eso que usamos la gema `dotenv` para leer y setear
variables de ambiente definidas en archivos al cargar nuestro proyecto.

El objetivo es que al clonar la app podamos correr los test y que estos pasen sin tener que
configurar nada.

* `.env.development`: En este archivo pondremos todas las variables que son necesarias para que la aplicación corra. Este archivo estara versionado junto con el proyecto, por lo que nunca pondremos valores sensibles como tokens, **API keys, passwords**, etc. En cambio podemos poner valores de referencia para que la aplicación pueda partir.

    ```plain text
    AWS_ACCESS_KEY=aws_access_key
    AWS_SECRET_ACCESS_KEY=aws_secret_access_key
    CHECK_THRESHOLD=10
    ```

* `.env.test`: En este archivo, si es necesario, pondremos las variables que deben ser diferentes en el ambiente de testing.

* `.env.local`: En este archivo pondremos todas las variables que tienen valores sensibles y que usaremos en nuestra máquina local para desarrollar. Este archivo esta listado en el `.gitignore`, por lo que no será versionado con el proyecto.

    ```plain text
    AWS_ACCESS_KEY=real_aws_access_key
    AWS_SECRET_ACCESS_KEY=real_aws_secret_access_key
    ```

### ¿ `ENV[]` o `ENV.fetch()`?

Podemos usar `ENV.fetch()` cuando queremos que la app lance una exception si esa variable no está seteada o si queremos asignar un valor por defecto usando el segundo paramentro.

```ruby
ENV.fetch('API_KEY') # Really important variable would trigger a keyError exception
ENV.fetch('CHANGE_THRESHOLD', 10) # Default value variable
```

> Nota: En desarrollo el uso de fetch sin un valor por defecto se va a gatillar sólo cuando
no hemos agregado ese key al archivo .env.development, esto es un buen recordatorio de esta práctica de documentación.
En ambientes de producción no se usa el archivo .env.development por lo que el uso de fetch nos ayudará a encontrar errores por falta de configuracion.

### ¿ `RACK_ENV` o `RAILS_ENV`?

Al crear la aplicación en heroku, se setea la variable `RACK_ENV=production` para declarar que
usaremos el environment `production` de la aplicación. No es necesario usar `RAILS_ENV` ya que
cuando se llama a `Rails.env` este hace fallback a RACK_ENV cuando RAILS_ENV no esta seteado.

Para que esta convención funcione, se debe usar el metodo `Rails.env` para chequear el ambiente.

### Heroku

En el caso de ambientes deployados en heroku, como `staging` y `production` estas variables son seteadas usando el CLI de heroku.

```bash
heroku config
heroku config:set KEY=value
heroku config:unset KEY
```

Esto hace que los keys seteados esten en el ambiente de heroku, por lo que la app
no va a tener problemas al tratar de obtenerlas usando `ENV[] o ENV.fetch()`

> Nota: El environment de deployment es diferente al environment de la aplicación. Para publicaren heroku, siempre usamos el environment production de la aplicacion, ya sea para deployar a staging o production.

> Nota:  La aplicación en heroku no usará los valores que estan en .env.development


