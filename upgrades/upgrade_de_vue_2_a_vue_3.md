# Upgrade de Vue 2 a Vue 3

1. Hacer tests de sistema para los flujos más importantes que tengan componentes de Vue.

1. Instalar la versión de compatibilidad de vue 3, siguiendo las instrucciones en [https://v3-migration.vuejs.org/migration-build.html](https://v3-migration.vuejs.org/migration-build.html) hasta el punto 3 (usar las instrucciones de webpack). Ignorar warnings de peer dependencies.

```plain text
// config/webpack/environment.js
// ...
environment.config.merge({
  resolve: {
    alias: {
      'vue': '@vue/compat/dist/vue.esm-browser.js',
    },
  },
});
```

```plain text
// config/webpack/loaders/vue.js

module.exports = {
  test: /\\.vue$/,
  loader: 'vue-loader',
  options: {
    compilerOptions: {
      compatConfig: {
        MODE: 2,
      },
    },
  },
};
```

1. Ejecutar `./bin/webpack-dev-server` en la carpeta del proyecto e ir resolviendo uno a uno los warnings y errores hasta que compile y los tests pasen.

1. Buscar alternativas a librerías usadas o actualizarlas.

    * vee-validate 3 -> vee-validate 4 (no tiene guía de migración, el autor recomienda tratar vee-validate 4 como una librería nueva)

    * vue-js-modal -> modal de [HeadlessUI](https://headlessui.com/)

    * v-tooltip -> [floating-vue](https://github.com/Akryum/floating-vue)

    * [vuex 3 -> vuex 4](https://vuex.vuejs.org/guide/migrating-to-4-0-from-3-x.html) (en un proyecto de cero recomendaría Pinia pero en un upgrade no vale la pena cambiar)

    * [vue-router 3 -> vue-router 4](https://router.vuejs.org/guide/migration/)

1. `npm ls vue` en la raiz del proyecto debería retornar *solo* vue 3.

```bash
❌
❯ npm ls vue
app@0.1.0 /project
├── vue@3.2.37
└─┬ vue-linkify@1.0.1
  └── vue@2.7.8

✅
❯ npm ls vue
app@0.1.0 /project
└── vue@3.2.37
```

1. Una vez que la app funcione, no necesariamente al 100% pero por lo menos que el trabajo de migración esté semicompleto, romper todo de nuevo instalando la receta de frontend de potassium. Acá hay tres alternativas:

    * Crear un proyecto nuevo con potassium en otra carpeta y comparar los cambios con lo que tienen, usando algo como [Beyond Compare](https://www.scootersoftware.com/) o [Meld](https://meldmerge.org/), para ir actualizando a mano.

    * Instalar la receta de frontend encima del proyecto, forzando la sobreescritura y revisar qué archivos sobran. Tiene la desventaja que hay que estar revisando el diff de git para ver qué cosas del proyecto cambiaron.

    * Instalar shakapacker a mano, siguiendo la [guía de migración](https://github.com/shakacode/shakapacker/blob/master/docs/v6_upgrade.md) y después revisar los cambios que hace la receta de frontend para que quede igual que el resto de los proyectos.

1. **No es necesario reescribir los componentes ya existentes a typescript**, se pueden ir migrando de a poco.

1. **No es necesario reescribir los componentes ya existentes a la Composition API + ****`script setup`**, se pueden ir migrando de a poco.

1. Al final de esto el proyecto debería quedar con Vue 3 (sin el build de compatibilidad a menos que no hayan encontrado alternativa a las librerías usadas), Tailwind 3 y Shakapacker.







### Comentarios Kalio

1. Los filtros ya no existen en vue3 (con la compatibility build tampoco funcionaban, al menos que haya hecho algo mal XD). lo que hice fue:

    * definir los filtros como funciones globales:

        ```javascript
        app.config.globalProperties.$filters = {
            camelizeKeys,
            camelize,
            deaccentisize,
            capitalize,
          };
        ```

    * luego se pueden usar asi: `:startup="$filters.camelizeKeys(<%= @startup.to_json %>)"`

    * en caso de que el proyecto use filtros nesteados, por ej. `filtro1 | filtro2 | filtro3`, se puede definir una función que los aplique en orden:

        ```javascript
        export function filterChain(value, filters) {
          if (!value) return null;
        
          let result = value;
          filters.forEach((filter) => {
            result = filter(result);
          });
        
          return result;
        }
        ```

        y luego se puede usar asi:

        ```javascript
        $filters.filterChain(state, [$filters.capitalize, $filters.split])
        ```

1. En vue3 `v-bind="$attrs"` reemplaza a `v-on="$listeners`. Si se quiere agregar algun input custom o modificar alguno, se puede hacer asi:

    ```javascript
    attrs() {
        return {
          ...this.$attrs,
          onInput: (event) => this.$emit('update:modelValue', event.target.value),
        };
      },
    ```

    notar que los eventos vienen prefixeados con `on`

1. `beforeDestroy` esta deprecated, no se si con la compatibility build sigue funcionando, en vue3 hay que remplazarlo por `beforeUnmount`

1. Si se usaba un EventBus con `Vue`, se puede reemplazar por algo como https://github.com/developit/mitt, o [https://vueuse.org/core/useEventBus/](https://vueuse.org/core/useEventBus/)
