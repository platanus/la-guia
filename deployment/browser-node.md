## Browser and Node (Open Source)

Siempre que crees una nueva librería invierte tiempo en buscar un buen nombre (compartelo con el equipo)

### `package.json`

En el archivo `package.json` debemos llenar al menos los siguientes campos.

```js
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
```

### Publicación

Los paquetes deben ser publicados en [NPM](http://npmjs.com).

#### NPM

En NPM debes registrar el paquete y publicar directamente cada version. La primera vez nos va a pedir nuestras credenciales de npmjs

    npm publish

> **IMPORTANTE:** Las publicaremos con nuestro usuario personal pero debemos agregar como owner al usuario de [platanus](https://npmjs.com/~platanus).

    # Agregar un owner a un package
    npm owner add platanus <package-name>

#### Publicando con el generador

El generador define un archivo [gulpfile](https://github.com/platanus/generator-angular-library/blob/master/generators/app/templates/base/gulpfile.js) con una serie de tareas para hacer más simple la publicacion.

Cuando quieras publicar una nueva version de una libreria simplemente debes hacer:

    gulp build
    gulp publish-npm
