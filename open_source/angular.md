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
  {% npmBowerPackage 'angular-doc-preview' %}{% endnpmBowerPackage %}
  {% npmBowerPackage 'angular-inflector', github='iobaixas/angular-inflector' %}{% endnpmBowerPackage %}
  {% npmBowerPackage 'angular-keep-values' %}{% endnpmBowerPackage %}
  {% npmBowerPackage 'angular-pallet-bundle' %}{% endnpmBowerPackage %}
  {% npmBowerPackage 'angular-pallet' %}{% endnpmBowerPackage %}
  {% npmBowerPackage 'angular-pretty-load' %}{% endnpmBowerPackage %}
  {% npmBowerPackage 'angular-progress' %}{% endnpmBowerPackage %}
  {% npmBowerPackage 'angular-restmod' %}{% endnpmBowerPackage %}
  {% npmBowerPackage 'angular-rut' %}{% endnpmBowerPackage %}
  {% npmBowerPackage 'angular-validate' %}{% endnpmBowerPackage %}
  {% npmPackage 'generator-platanus-angular-library', github='platanus/generator-angular-library' %}{% endnpmPackage %}
  {% npmPackage 'generator-platanus-ionic' %}{% endnpmPackage %}
  {% npmBowerPackage 'platanus-angular-auth', github='platanus/angular-auth' %}{% endnpmBowerPackage %}
  {% npmBowerPackage 'platanus-angular-push', github='platanus/angular-push', name="Angular Push" %}{% endnpmBowerPackage %}
{% finishPackagesTable %}{% endfinishPackagesTable %}
