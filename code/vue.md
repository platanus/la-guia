Vue
===

*Vue.js* es un framework **progresivo** orientado a la construcción de interfáces de usuario, 
siendo capáz también de servir en la contrucción de SAP (*Single Page Applications*). 

Es una herramienta que opera sobre Javascript, y en particular en *Platan.us* nos gusta la 
forma en la que se puede trabajar con el bajo el dialecto ES2015. 

## Prerequisites

Será necesario desactivar la gema **turbolinks**.

## Setup

Para utilizar este framework con *Rails* haremos uso de la siguiente Gema,

```Ruby
gem "browserify-rails"
```

La cual permite trabajar con *CommonJS* directamente desde el interior del *asset pipeline* de 
Rails. Luego será necesario incluir un archivo `package.json` en la raiz de nuestro proecto 
para trabajar con algunos módulos **npm**.

```Javascript
{
  "name": "some-name",
  "dependencies" : {
    "browserify": "~10.2.4",
    "browserify-incremental": "~3.0.1",
    "babel-preset-es2015": "~6.16.0",
    "babelify": "~7.3.0",
    "vue": "~1.0.28"
  },
  "engines": {
    "node": ">= 0.10"
  }
}
```

Luego será necesario indicarle a *Rails* que incluya la carpeta `node_modules` dentro del 
asset pipeline, para lo cual es necesario incluir la siguiente línea

```Ruby
# config/application.rb
config.browserify_rails.commandline_options = "-t [ babelify --presets [ es2015 ] ]"
```

Un initializer le dirá al assets pipeline que debe considerar al archivo `app.js` dentro del
proceso de compilación.

```Ruby
# config/initializers/assets.rb
Rails.application.config.assets.precompile += %w( app.js )
```

Luego se incluye el archivo `app.js` dentro del layout de la aplicación con la opción *defer*

```
<%= javascript_include_tag 'app', :defer => 'true' %>
```

Finalmente se instalan los módulos javascript, y el mismo framework Vue

```
$ npm install
```

## Usage

El código de nuestra aplicación estará ubicado en el directorio `app/assets/javascript/`, su 
extensión será `.js` y estará programado en ES2015.

Una forma de ilustrar esto será creando un componente bastante básico que simplemente realice 
el rendering de cierto contenido.

```Javascript
// app/assets/javascript/app.js
import Vue from "vue";
var app = new Vue({
  el: '#app',
  data: {
    message: 'Hello Vue!'
  }
});
```

Finalmente en cualquiera de las vistas de nuestra aplicación podremos instanciar este componente 

```html
<div id="app">
  {{ message }}
</div>
```
