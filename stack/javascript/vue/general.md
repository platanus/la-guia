# General

[Vue.js](https://vuejs.org/v2/guide/) es un framework progresivo orientado a la construcción de interfaces de usuario, siendo capaz también de servir en la contrucción de SPAs.

La documentación es excelente y debería ser el primer paso en caso de cualquier duda.

### Tips

La regla en general en Platanus es que solo puede haber una fuente de la verdad para el estado de la aplicación, determinada por quién maneja la paginación.

* Si la paginación la maneja Rails, Rails debería manejar el estado de la aplicación, siendo Vue un suplemento para agregar elementos dinámicos.

* Si la paginación la maneja Vue (mediante un router, por ejemplo), el estado de la aplicación lo debería manejar Vue (con Vuex probablemente), usando Rails principalmente como API.

Hay casos en que se pueden mezclar, como por ejemplo un componente complejo (un wizard o tour, por ejemplo) puede tener su propio estado interno dentro de una página de Rails, pero lo ideal es elegir uno de estos caminos por proyecto para evitar confusiones.


