# Testing

Para realizar tests unitarios dentro de Rails y Vue se puede usar [Jest](https://jestjs.io/) y [Vue Test Utils](https://vue-test-utils.vuejs.org/). Como siempre, la [documentación de Vue](https://vuejs.org/v2/guide/unit-testing.html) es un buen punto de partida.

En proyectos nuevos Potassium ya incluye los archivos y la configuración necesaria para empezar a testear. En proyectos antiguos sigue los siguientes pasos para tener un ambiente listo para realizar pruebas de manera local.

```bash
> yarn add jest vue-jest babel-jest @vue/test-utils jest-serializer-vue babel-core@^7.0.0-bridge.0 --dev
```

```json
// https://vue-test-utils.vuejs.org/guides/#testing-single-file-components-with-jest
package.json
  "scripts": {
    "test": "jest",
    "test:watch": "jest --watch"
  },
  "jest": {
    "roots": [
      "app/javascript"
    ],
    "moduleDirectories": [
      "node_modules",
      "app/javascript"
    ],
    "moduleNameMapper": {
      "^@/(.*)$": "app/javascript/$1"
    },
    "moduleFileExtensions": [
      "js",
      "json",
      "vue"
    ],
    "transform": {
      "^.+\\\\.js$": "<rootDir>/node_modules/babel-jest",
      ".*\\\\.(vue)$": "<rootDir>/node_modules/vue-jest"
    },
    "snapshotSerializers": [
      "<rootDir>/node_modules/jest-serializer-vue"
    ]
  },
```

---

Para ejecutar los tests en un ambiente CI (como CircleCI), copia los cambios correspondientes de [bin/ci_build](https://github.com/platanus/potassium/blob/ce9aa9e1ddd19c344b74afe5dfa3a4c7af866176/lib/potassium/assets/bin/cibuild.erb), [.circleci/config.yml](https://github.com/platanus/potassium/blob/ce9aa9e1ddd19c344b74afe5dfa3a4c7af866176/lib/potassium/assets/.circleci/config.yml.erb) y [docker-compose.ci](https://github.com/platanus/potassium/blob/ce9aa9e1ddd19c344b74afe5dfa3a4c7af866176/lib/potassium/assets/docker-compose.ci.yml)

---

```bash
> yarn test
 FAIL  config/webpack/test.js
  ● Test suite failed to run

    Your test suite must contain at least one test.
```

Un ejemplo de test básico usando el componente que instala el setup:

`App.vue`

```javascript
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

```javascript
import { shallowMount } from '@vue/test-utils';
import App from './app.vue';

describe('app.vue', () => {
  it('displays message on load', () => {
    const wrapper = shallowMount(App);
    expect(wrapper.find('p').text()).toEqual('Hello Platanus!');
  });
});
```

```plain text
 PASS  app/javascript/app.spec.js
  app.vue
    ✓ displays message on load (25ms)
```


