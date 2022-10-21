# Redux

En React y React Native se suele utilizar el concepto de store. Este es un estado global de la aplicación en el que se mantienen datos que se ocupan en varios lugares y por lo tanto no pertenecen al estado de un solo componente. Para esto ocupamos [Redux](https://redux.js.org/), posiblemente la herramienta más conocida y utilizada para esto.

Hay algunas herramientas complementarias que probablemente necesites:

* [Redux toolkit](https://redux-toolkit.js.org/): "redux con las baterias incluidas", este paquete incluye varias funciones para agilizar el desarrollo con Redux. Incluye funcionalidades que cumplen tareas muy comunes como configurar el store, crear una acción para cada reducer, o separar tu store en convenientes [slices](https://redux-toolkit.js.org/usage/usage-guide#creating-slices-of-state).

* [Redux saga](https://redux-saga.js.org/): redux no tiene la capacidad de manejar acciones asíncronas en sus reducers. Esto es sin embargo una funcionalidad muy usada, por ejemplo para obtener datos del servidor y luego guardarlos en el store. Para suplir esta falencia nace Redux Saga. Esta librería ocupa [generadores](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/function*) y una de sus grandes cruzadas es ser testeable, que en general no es fácil con funciones asíncronas. Es altamente probable que lo necesites.
