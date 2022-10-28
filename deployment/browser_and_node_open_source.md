# Browser and Node (Open Source)

Siempre que crees una nueva librería invierte tiempo en buscar un buen nombre (compártelo con el equipo)

## Crear el paquete

Primero, [crea un repositorio en Platanus](https://github.com/organizations/platanus/repositories/new). Asegúrate de que sea público.

Luego puedes crear el paquete con `npm init`, el cual te preguntará algunos detalles del paquete, o crear manualmente un `package.json`.

### `package.json`

En el archivo `package.json` debemos llenar al menos los siguientes campos.

```json
//restmod package.json example
{
  "name": "angular-restmod",
  "description": "API Bound Models for AngularJS",
  "version": "1.11.1",
  "dependencies": {
    ...
  },
  "devDependencies": {
    ...
  }
}
```

Puedes agregar como autor a Platanus y a ti como colaborador:

```json
"author": {
    "name": "Platanus",
    "url": "https://platan.us"
  },
"contributors": [
  {
    "name": "Tu nombre"
  }
],
```

## Dependencias

Al menos deberías instalar las siguientes dependencias:

* `jest`: Testing

* `eslint`: Linter. Puedes copiar las [reglas](https://github.com/platanus/potassium/blob/master/lib/potassium/assets/.eslintrc.json) que vienen en Potassium. Puede ser necesario eliminar las reglas relacionadas a Vue y Tailwind si no serán utilizados.

* `eslint-plugin-import`: Permite utilizar el linter para revisar sintaxis de import/export.

Opcionalmente, puedes instalar:

* `eslint-plugin-jest`: en caso de no instalarlo reemplazar `jest/globals` en `env` de `.eslintrc.json` por solo `jest`.

## ES6 vs CommonJS export sintaxis

Si estamos desarrollando un paquete de Node (y no de web) debemos usar la sintaxis de CommonJS para imports/exports:

```javascript
// lib.js
module.exports = {
  funcionLib
}
```

```javascript
// otro.js
const lib = require('./lib')
```

Se puede utilizar esta sintaxis manualmente o utilizar la sintaxis de ES6 y luego compilar con un bundle como webpack para que la librería quede en formato CommonJS.

## Comandos de consola personalizados para funciones del paquete

Para correr algún archivo con un comando personalizado se deben seguir los siguientes pasos:

* Crear un archivo `cli.js` o una carpeta `cli` con archivos que queremos que se ejecuten.

* Al principio de estos archivos colocar:

    ```plain text
    #!/usr/bin/env node
    ```

* En `package.json` agregar una sección de:

    ```json
    "bin": {
      "nombre-comando-cli": "archivo.js",
      "nombre-comando-cli-2": "archivo2.js",
    }
    ```

Correr `npm link` (permite simular la instalación del paquete), reiniciar la terminal y probar corriendo alguno de los comandos creados.
Si se modifica alguno de los archivos javascript no es necesario volver a correr `npm link`.

## Publicación

Los paquetes deben ser publicados en [NPM](http://npmjs.com/). Si no la tienes, deberás crearte una cuenta personal.

### NPM

Si no has iniciado sesión en tu cuenta desde tu terminal, corre:

```bash
npm login
```

En NPM debes registrar el paquete y publicar directamente cada versión.

```bash
npm publish
```

> IMPORTANTE: Las publicaremos con nuestro usuario personal pero debemos agregar como owner al usuario de platanus.

```bash
# Agregar un owner a un package
npm owner add platanus-owner <package-name>
```


