Vue
===

*[Vue.js](https://vuejs.org/v2/guide/)* es un framework **progresivo** orientado a la construcción de interfáces de usuario,
siendo capáz también de servir en la contrucción de SAP (*Single Page Applications*).

La documentación es excelente y debería ser el primer paso en caso de cualquier duda.

## Setup

Gracias a Webpacker y [Potassium](https://github.com/platanus/potassium) es muy fácil empezar a usar Vue junto con un proyecto nuevo de Rails.

```bash
> potassium create PROYECTO_DE_RAILS
[...]
Which front-end framework are you going to use?:
‣ Vue
  Angular
  None
```

En el caso que tengas que agregar Vue a un proyecto Rails 5 que ya fue inicializado, puedes usar webpacker para [configurarlo](https://github.com/rails/webpacker#vue):

```
bundle exec rails webpacker:install:vue
```

En Rails 5.2+ hay que configurar el ambiente de desarrollo con lo siguiente:

`config/initializers/content_security_policy.rb`
```Ruby
Rails.application.config.content_security_policy do |policy|
  if Rails.env.development?
    policy.connect_src :self, :https, 'http://localhost:3035', 'ws://localhost:3035'
    policy.script_src :self, :https, :unsafe_eval
  else
    policy.script_src :self, :https
  end
end
```

Una vez instalado, la estructura inicial del proyecto será la siguiente:

```
app/javascript:
  ├── packs:
  │   └── application.js
  └── app.vue
```

```
views/layouts/application.html.erb

<%= javascript_pack_tag 'application' %>
<%= stylesheet_pack_tag 'application' %>
```

Webpacker funciona con _packs_, siendo cada "pack" un bundle de código independiente generado por webpack. En este caso, agregando las tags de arriba la aplicación de Rails va a tener acceso al compilado de todo lo importado en `application.js` y el `scss` generado a partir de los archivos `.vue`.

En un proyecto más avanzado esta sería una estructura ejemplo:
```
app/javascript:
  ├── main
  │   ├── api
  │   │   └── form-api.js
  │   ├── components:
  │   │   ├── sidebar.vue
  │   │   ├── main.vue
  │   │   └── footer.vue
  ├── shop
  │   ├── api
  │   │   └── cart-api.js
  │   ├── components:
  │   │   ├── cart.vue
  │   │   └── cart.spec.js
  │   ├── store:
  │   │   └── index.js
  │   │   └── modules
  │   │       ├── cart.js
  │   │       └── cart.spec.js
  └── packs:
      ├── main.js
      └── shop.js
```

```
views/layouts/application.html.erb

<%= javascript_pack_tag 'main' %>
<%= stylesheet_pack_tag 'main' %>
```

```
views/layouts/shop.html.erb

<%= javascript_pack_tag 'shop' %>
<%= stylesheet_pack_tag 'shop' %>
```

## Tips

La regla en general en Platanus es que solo puede haber una fuente de la verdad para el estado de la aplicación, determinada quién maneja la paginación.
- Si la paginación la maneja Rails, Rails debería manejar el estado de la aplicación, siendo Vue un suplemento para agregar elementos dinámicos.
- Si la paginación la maneja Vue (mediante un router, por ejemplo), el estado de la aplicación lo debería manejar Vue (con Vuex probablemente), usando Rails principalmente como API.

Hay casos en que se pueden mezclar, como por ejemplo un componente complejo (un wizard o tour, por ejemplo) puede tener su propio estado interno dentro de una página de Rails, pero lo ideal es elegir uno de estos caminos por proyecto para evitar confusiones.

## Linting

Usamos [eslint-plugin-vue](https://vuejs.github.io/eslint-plugin-vue) con las reglas [Strongly Recommended](https://vuejs.github.io/eslint-plugin-vue/rules/#priority-b-strongly-recommended-improving-readability) activadas en el mono.

## Tests

Para realizar tests unitarios dentro de Rails y Vue se puede usar [Jest](https://jestjs.io/) y [Vue Test Utils](https://vue-test-utils.vuejs.org/). Como siempre, la [documentación de Vue](https://vuejs.org/v2/guide/unit-testing.html) como puntos de partida.

Potassium no incluye la configuración para realizar tests todavía pero con los siguientes pasos vas a tener un ambiente listo para realizar pruebas.

```bash
> yarn add jest @vue/test-utils vue-jest babel-core babel-jest --dev
```

```
// https://vue-test-utils.vuejs.org/guides/#testing-single-file-components-with-jest
package.json
  "scripts": {
    "test": "jest"
    "test:watch": "jest --watch"
  },
  "jest": {
    "roots": [
      "app/javascript"
    ],
    "moduleFileExtensions": [
      "js",
      "json",
      "vue"
    ],
    "transform": {
      "^.+\\.js$": "babel-jest",
      ".*\\.(vue)$": "vue-jest"
    }
  },
```

```bash
> yarn test
 FAIL  config/webpack/test.js
  ● Test suite failed to run

    Your test suite must contain at least one test.
```

Un ejemplo de test básico usando el componente que instala el setup:

`App.vue`
```
<template>
  <div id="app">
    <p>{{ message }}</p>
  </div>
</template>

<script>
export default {
  data: function () {
    return {
      message: "Hello Platanus!"
    }
  }
}
</script>

<style scoped>
p {
  font-size: 2em;
  text-align: center;
}
</style>

```

`App.spec.js`
```
import { shallowMount } from '@vue/test-utils';
import App from './app.vue';

describe('app.vue', () => {
  it('displays message on load', () => {
    const wrapper = shallowMount(App);
    expect(wrapper.find('p').text()).toEqual('Hello Platanus!');
  });
});
```

```bash
 PASS  app/javascript/app.spec.js
  app.vue
    ✓ displays message on load (25ms)
```
