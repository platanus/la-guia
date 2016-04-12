# Angular (Open Source)

- Para no partir de cero, usamos [Yeoman](http://yeoman.io/) con el [generador de platanus](https://github.com/platanus/generator-angular-library)
- Invierte tiempo en buscar un buen nombre (compartelo con el equipo)

Para crear el package con el generador simplemente haz:

    mkdir my-library
    cd my-library
    yo platanus-angular-library

#### Spec `package.json`

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

## Publicación

Las paquetes deben ser publicados en dos lugares. [Bower](https://bower.io) y [NPM](http://npmjs.com).

#### Bower

En bower solo se debe registrar el nombre del paquete la primera vez y asociarlo a un endpoint en github.

    bower register <package-name> <git-endpoint>

Luego en github se debe crear un tag con el nombre de la version a publicar.

#### NPM

En NPM debes registrar el paquete y publicar directamente cada version. La primera vez nos va a pedir nuestras credenciales de npmjs

    npm publish

> **IMPORTANTE:** Las publicaremos con nuestro usuario personal pero debemos agregar como owner al usuario de [platanus](https://npmjs.com/~platanus).

    # Agregar un owner a un package
    npm owner add platanus <package-name>

##### Publicando con el generador

El generador define un archivo [gulpfile](https://github.com/platanus/generator-angular-library/blob/master/generators/app/templates/base/gulpfile.js) con una serie de tareas para hacer más simple la publicacion.

Cuando quieras publicar una nueva version de una libreria simplemente debes hacer:

    gulp build
    gulp publish-npm

## Un listado de librerías Platanus

{% packagesTable %}{% endpackagesTable %}
  {% npmPackage 'angular-restmod' %}{% endnpmPackage %}
  {% npmPackage 'angular-pallet-bundle' %}{% endnpmPackage %}
  {% npmPackage 'angular-rut' %}{% endnpmPackage %}
  {% npmPackage 'platanus-angular-push', github='platanus/angular-auth', name="Angular Push" %}{% endnpmPackage %}
  {% npmPackage 'angular-auth' %}{% endnpmPackage %}
  {% npmPackage 'angular-pretty-load' %}{% endnpmPackage %}
  {% npmPackage 'angular-keep-values' %}{% endnpmPackage %}
  {% npmPackage 'angular-validate' %}{% endnpmPackage %}
  {% npmPackage 'angular-doc-preview' %}{% endnpmPackage %}
  {% npmPackage 'angular-progress' %}{% endnpmPackage %}
  {% npmPackage 'angular-pallet' %}{% endnpmPackage %}
{% finishPackagesTable %}{% endfinishPackagesTable %}
