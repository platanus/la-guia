## Testing

Para realizar tests unitarios dentro de Rails y Vue se puede usar [Jest](https://jestjs.io/) y [Vue Test Utils](https://vue-test-utils.vuejs.org/). Como siempre, la [documentación de Vue](https://vuejs.org/v2/guide/unit-testing.html) es un buen punto de partida.

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
