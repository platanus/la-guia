# Upgrade de Postgresql

original: 

## Índice

1. [Intro](#intro)

1. [Pasos a seguir](#pasos-a-seguir)

1. [Resumen](#resumen)

# Intro

Caso de ejemplo: *del 10 al 11*

**Base de datos a upgradear**: [postgresql-elliptical-29911](https://data.heroku.com/datastores/d7e170f0-29ee-4f26-ae26-fb7eaeca7ed7)

El upgrade de postgres, en este caso debe hacerse con el comando `pg:upgrade`, el cual se usa solamente para hacer un upgrade de una “follower database”. Debido a que la que vamos a actualizar es una base de datos tipo `leader` y `primary`, se debe considerar hacer varios pasos antes de hacer el upgrade.

> 💡 OJO esto es solo para bds que no son del tipo hobbie-tier y que pesan más de 10gb!

# Pasos a seguir

Según [https://devcenter.heroku.com/articles/upgrading-heroku-postgres-databases#upgrading-with-pg-upgrade](https://devcenter.heroku.com/articles/upgrading-heroku-postgres-databases#upgrading-with-pg-upgrade)

### 1. Proveer una follower database

Para empezar, hay que crear un nuevo follower para la base de datos, y esperar a que se “ponga al día” con al leader. 

```bash
**$ heroku addons:create ****heroku-postgresql:standard-0**** --follow ****HEROKU_POSTGRESQL_WHITE_URL**** --app pl-rutt-production**
# te va a salir lo siguiente:
Adding heroku-postgresql:standard-0 to pl-rutt-production... done, v71 ($200/mo)
Attached as HEROKU_POSTGRESQL_XX
Follower will become available for read-only queries when up-to-date
Use `heroku pg:wait` to track status
# ------

**$ heroku pg:wait --app pl-rutt-production**
Waiting for database HEROKU_POSTGRESQL_XX_URL... available
```

La follower es considerada “caught up” cuando está dentro de los 200 commits o condirmaciones de la bbdd principal. Se puede veridicar cuántos commits tiene la follower usando el comando `pg:info`

```bash
**$ heroku pg:info --app pl-rutt-production**
=== HEROKU_POSTGRESQL_WHITE_URL, DATABASE_URL
Plan:                  Standard 0
Status:                Available
...
=== HEROKU_POSTGRESQL_PUCE_URL
Plan:                  Standard 0
Status:                Available
..
=== HEROKU_POSTGRESQL_XX
Plan:                  Standard 0
Status:                Available
...
Following: HEROKU_POSTGRESQL_WHITE (DATABASE_URL)
Behind By: 123 commits
```

### 2. Entrar en un modo mantención para prevenir las escrituras en la base de datos.

> 💡 El modo de mantenimiento no *"scale down"* automáticamente los dynos. Los dynos web y no web deben *"scale down"* (por ejemplo, heroku ps:scale worker=0) para garantizar que ninguna conexión esté escribiendo datos en la base de datos.

Es importante que ninguna data nueva sea escrita en la primary database ([postgresql-elliptical-29911](https://data.heroku.com/datastores/d7e170f0-29ee-4f26-ae26-fb7eaeca7ed7)) durante el proceso de upgrade, porque sino, esta no va a ser transferida a la nueva base de datos. Para lograr esto, hay que poner la bbdd en modo mantención. Si hay scheduler jobs ejecudandose, también se deben deshabilitar.

**Si tienes dynos asociados corriendo (web y no web)**

    Ejecuta el siguiente comando para saber la escala de los dynos:

    ```javascript
    **$ heroku ps --app pl-rutt-production
    **=== web (Standard-2X): bundle exec puma -C ./config/puma.rb (2)
    web.1: up 2022/10/19 23:04:41 -0300 (~ 3h ago)
    web.2: up 2022/10/19 04:29:34 -0300 (~ 22h ago)
    
    === worker (Standard-2X): bundle exec sidekiq (1)
    worker.1: up 2022/10/20 02:24:47 -0300 (~ 37m ago)
    ```

    Luego, debes ejecutar el siguiente comando para reducirlos:

    `heroku ps:scale web=0 worker=0 --app pl-rutt-production` 

```bash
**$ heroku maintenance:on --app pl-rutt-production
**Enabling maintenance mode for pl-rut-production... done
```

### 3. Actualizar las base de datos tipo follower

Ahora se puede actualizar la base de datos secundaria. Hay que esperar a que la follower database se ponga al día por completo con la principal, en la que debería decir que esta “behind by: 0 commits”.

```plain text
$ **heroku pg:info --app pl-rutt-production**
=== HEROKU_POSTGRESQL_WHITE_URL
Plan:        Standard 0
Status:      available
...
=== HEROKU_POSTGRESQL_XX_URL
Plan:        Standard 2
Status:      available
...
Following:   HEROKU_POSTGRESQL_WHITE_URL (DATABASE_URL)
Behind By:   **0 commits**
```

Si no se espera a que quede “caughted up” el upgrade te va a tirar el siguiente error (probablemente)

` ▸    database must not be too far behind leader, please wait until your follower catches up with its leader.`

Una vez que esté en el estado “caught up” se puede hacer el upgrade. Lo ideal es que, como estamos en la versión 10, la actualicemos a la 11.

Entonces, el comando sería más o menos así:

`heroku pg:upgrade ``HEROKU_POSTGRESQL_XX`` ``--version 11`` --app pl-rutt-production`



Este paso aproximadamente durará como 20 min en completarse. De hecho, sale que es aprox 3 min por gb, así que podrían ser en verdad 19,4 GB * 3 min/GB ≈ 60 minutos 🙀 

De todas maneras, se puede ver el progreso del upgrade con el comando

`heroku pg:wait --app pl-rutt-production`



### 4. Promote to Primary 🎉

Ahora se debe “promote” la bbdd recién actualizada para configurarla como la bbdd principal (`DATABASE_URL`). Para ello, se debe hacer un `pg:promote`, que también crea un archivo adjunto alternativo para la antigua base de datos princial, asignado con una nueva variable de configuración `HEROKU_POSTGRESQL_<color>_URL`. El proceso de *promotion* activa un lanzamiento y reinicia la app.

`heroku pg:promote HEROKU_POSTGRESQL_XX --app pl-rutt-production`



ahora la base de datos follower es la principal, aunque aún no recibe nuevas solicitudes.

Si la bbdd principal original se adjuntó a varias apps, se debe adjuntar la nueva bbdd de esas aplicaciones con la extensión `heroku addons:attach ...`.

> 💡 **IMPORTANTE!!! **Después del *promotion* los followers de la bbdd principal original NO comienzan a seguir de manera automática la nueva bbdd principal. Por lo que se debe crear nuevos followers para la nueva bbdd principal, según sea necesario:

    `heroku addons:create heroku-postgresql:standard-0/ --follow DATABASE_URL --app example-app`

### 5. Salir del modo mantención

Para reanudar el funcionamiento normal de la app, se debe volver a escalar los non-web dynos a sus niveles originales. (e.g., `heroku ps:scale worker=1` ).

**Si tienes dynos asociados que deben correr (web y no web)**

    Debes ejecutar el siguiente comando para volverlos a escalar:

    `heroku ps:scale ``web=2 worker=1`` --app pl-rutt-production` 

**Si la primaria que estás actualizando tenía followers**

    Tienes que volver a crear las followers que tenía. Ejemplo:

    `heroku addons:create heroku-postgresql:standard-0 --follow DATABASE_URL -a pl-rutt-production`

Finalmente, se apaga el maintenance mode, con el siguiente comando:

```javascript
**$ heroku maintenance:off --app pl-rutt-production**
```

Ahora la app puede recibir requests a la bbdd actualizada. Se puede confirmar esto ejecutando el comando `heroku pg:info --app pl-rutt-production`. La bbdd denotada por `DATABASE_URL` es la considerada como primaria. 

Finalmente, debes sacar las bases de datos deprecadas:

```bash
**$ heroku addons:destroy ****HEROKU_POSTGRESQL_WHITE**** --app pl-rutt-production**
```

---

# Resumen

Pasos a seguir:

- [ ] `heroku addons:create heroku-postgresql:standard-0 --follow HEROKU_POSTGRESQL_WHITE_URL --app pl-rutt-production`

    Te crea la bd follower en la que vas a copiar la BD primaria para hacer el upgrade.

    OJO es útil recordar el nombre de la nueva base de datos. En mi caso, se llamaba *HEROKU_POSTGRESQL_COPPER_URL*.

- [ ] `heroku pg:wait --app pl-rutt-production`

    este te ayuda a ver el estado de la bd que creaste

    **si tienes dynos corriendo** 

    - [ ] `heroku ps --app pl-rutt-production`

        este comando ayuda a ver la scale que se debe volver a poner una vez actualizada la bd.

        En mi caso me salió esto: 

        ```bash
        === web (Standard-2X): bundle exec puma -C ./config/puma.rb (2)
        web.1: up 2022/10/19 23:04:41 -0300 (~ 3h ago)
        web.2: up 2022/10/19 04:29:34 -0300 (~ 22h ago)
        
        === worker (Standard-2X): bundle exec sidekiq (1)
        worker.1: up 2022/10/20 02:24:47 -0300 (~ 37m ago)
        ```

        Así yo sabía que eran** 2 web** y **1 worker**.

    - [ ] `heroku ps:scale web=0 worker=0 --app pl-rutt-production`

        este permite que los workers dejen de funcionar, para que así no traten de escribir en la BD

- [ ] `heroku maintenance:on --app pl-rutt-production`

    con este comando vas a hacer que la app pase a estado de mantención, por lo que no se podrá escribir en la BD primaria

- [ ] `heroku pg:info --app pl-rutt-production`

    este comando te ayuda a ver si la base de datos que creaste está a la par con la primaria. ¿Cómo saberlo? debería decir esto debajo del nombre de la bd creada:

    ```bash
    Following:   HEROKU_POSTGRESQL_WHITE_URL (DATABASE_URL)
    Behind By:   **0 commits**
    ```

- [ ] `heroku pg:upgrade HEROKU_POSTGRESQL_COPPER_URL --version 11 --app pl-rutt-production`

    Este comando se debe ejecutar una vez que la bd creada está a la par con la primaria. Con este haces el upgrade. En mi caso se demoró unos 20 mins

- [ ] `heroku pg:wait --app pl-rutt-production`

    este comando te ayudará a ver el progreso del update

- [ ] `heroku pg:promote HEROKU_POSTGRESQL_COPPER_URL --app pl-rutt-production`

    este comando hace que ahora la bd primaria es la creada y actualizada.

    **[ANTES DE APAGAR EL MODO MANTENCIÓN] si tienes dynos:**

    - [ ]  `heroku ps:scale web=2 worker=1 --app pl-rutt-production`

        Con este comando volví a la escala que tenía antes.

    **Si tienes en un principio la primaria tenía una bd follower **

    - [ ] `heroku addons:create heroku-postgresql:standard-0 --follow DATABASE_URL -a pl-rutt-production`

        tienes que crear una follower nueva. CREO que no es necesario esperar a que se copie entera para apagar la mantención, pero quizá mejor esperar (por si las 🪰🪰)

- [ ] `heroku maintenance:off --app pl-rutt-production`

    Y ahí vuelve todo a la normalidad

- [ ] `heroku addons:destroy HEROKU_POSTGRESQL_WHITE --app pl-rutt-production`

    OJO: hay que sacar dps las bases de datos que sobren, es decir, las antiguas. En mi caso, la primaria ERA *HEROKU_POSTGRESQL_WHITE* así que tengo que eliminar esta.

LISTOOOO!! 🎉🎉
