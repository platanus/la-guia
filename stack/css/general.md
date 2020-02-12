## General

Cuando comenzamos a añadir estilo, debemos preguntarnos dos cosas: 1. *¿Cómo organizo los componentes existiran?* y 2. *¿Cómo nombro mis componentes?*. El objetivo de esta sección es dar una idea general de cómo responderlas.

### 1. ¿Cómo organizo los componentes que existiran?

Primero tratamos de evaluar la visión general de la vista que estamos trabajando. Para eso nosotros preferimos usar [Grid](https://css-tricks.com/snippets/css/complete-guide-grid/).
Sin embargo, cuando nuestros componentes se van volviendo más especificos o anidados, Grid puede ser demasiado verboso. Por eso lo complementamos con [Flexbox](https://css-tricks.com/snippets/css/a-guide-to-flexbox/).

Con estas dos formas en mente para armar nuestros *layouts* podemos avanzar a la siguiente pregunta.

### 2. ¿Cómo nombro mis componentes?

Usamos la metodología de [BEM](http://getbem.com/) (Block, Element, Modifier). La gracia de BEM es que vuelve más evidente la relación entre el HTML y el CSS que se esta construyendo.

En la metodología BEM un *block* es la abstracción a más alto nivel de un componente. Tiene significado por si mismo. Algunos ejemplos típicos son un botón (`.btn {} `), un menu de navegación (`.nav {}`), un formulario (`.form {}`).

Los *blocks* estan conformados por *elements*. Un *element* no tiene significado por si mismo. Para nombrarlos tomamos el nombre del *block* padre y separamos con 2 *underscores* ( _ ) lo que es. Algunos ejemplos típicos en la misma línea que los ya nombrados, el icono de un botón (`.btn__icon {}`), un link en el menu de navegación (`.nav__link {}`), un input en un formulario (`.form__input {}`).

Finalmente los *modifiers* son simples *flags* sobre un *block* o *element* cuyo proposito es cambiar la apariencia, el comportamiento o el estado. Se nombran usando el nombre del *block* o *element* seguido de 2 guiones. Algunos ejemplos serían: un botón que es rojo (`.btn--red`) o un icono de un botón que es rojo (`btn__icon--red`), un link desactivado en el menu de navegación (`.nav__link--deactivated {}`), un formulario cuya forma es alargada (`.form--wide`).

Basado en [getbem.com](http://getbem.com/) y [css-tricks](https://css-tricks.com/bem-101/).
