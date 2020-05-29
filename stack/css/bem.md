## BEM

BEM (Block, Element, Modifier) es una convención para nomenclatura de clases en HTML y CSS, con el objetivo de estandarizar la forma en que se escribe CSS dentro de un proyecto y entre varios equipos.

### ¿Por qué lo usamos?

En Platanus trabajamos en múltiples proyectos al mismo tiempo, cada uno con múltiples desarrolladores que pueden cambiar en el tiempo. Se hacía imperativo aplicar un estándar de CSS para facilitar la mantenibilidad en el tiempo y BEM llenó ese espacio, dando orden al caos de la cascada.

Estos días la utilidad de BEM se ha visto reducida gracias a nuestro uso de Tailwind CSS (que resuelve el problema mediante la eliminación del CSS personalizado) pero hay casos en que todavía es útil (creación de grupo de clases de Tailwind CSS o en la mantención de proyectos antiguos).


#### ¿Cómo lo usamos?

Usamos la metodología de BEM (Block, Element, Modifier). La gracia de BEM es que vuelve más evidente la relación entre el HTML y el CSS que se esta construyendo.

En la metodología BEM un block es la abstracción a más alto nivel de un componente. Tiene significado por si mismo. Algunos ejemplos típicos son un botón (`.btn`), un menu de navegación (`.nav`), un formulario (`.form`).

Los blocks estan conformados por elements. Un element no tiene significado por si mismo. Para nombrarlos tomamos el nombre del block padre y separamos con 2 underscores ( `_` ) lo que es. Algunos ejemplos típicos en la misma línea que los ya nombrados, el icono de un botón (`.btn__icon`), un link en el menu de navegación (`.nav__link `), un input en un formulario (`.form__input`).

Finalmente los modifiers son simples flags sobre un block o element cuyo propósito es cambiar la apariencia, el comportamiento o el estado. Se nombran usando el nombre del block o element seguido de 2 guiones. Algunos ejemplos serían: un botón que es rojo (`.btn--red`) o un icono de un botón que es rojo (`btn__icon--red`), un link desactivado en el menu de navegación (`.nav__link--deactivated `), un formulario cuya forma es alargada (`.form--wide`).

#### Ejemplo
```html
<div class="profile">
  <img src="https://source.unsplash.com/random/100x100" class="profile__avatar">
  <div>
    <h1 class="profile__name">Lorem Ipsum</h1>
    <p class="profile__description">Lorem ipsum dolor sit, amet consectetur adipisicing elit. Expedita quibusdam quos exercitationem et facere soluta, voluptatibus nobis tempore non fugit, similique cumque quod placeat, sit blanditiis mollitia sint perferendis eum.</p>
  </div>
  <div class="profile__likes">
    <img src="https://source.unsplash.com/random/20x20" class="profile__like">
    <img src="https://source.unsplash.com/random/20x20" class="profile__like">
    <img src="https://source.unsplash.com/random/20x20" class="profile__like">
    <img src="https://source.unsplash.com/random/20x20" class="profile__like">
    y 4 personas más
  </div>
</div>
```
### Recursos Útiles

- [BEM 101](https://css-tricks.com/bem-101/) y [Battling BEM CSS: 10 Common Problems And How To Avoid Them](https://www.smashingmagazine.com/2016/06/battling-bem-extended-edition-common-problems-and-how-to-avoid-them/): Estos artículos incluyen casi todas las preguntas básicas y frecuentes de cómo se usa BEM.
- [BEM Cheatsheet](https://9elements.com/bem-cheat-sheet/): Ejemplos de varios componentes en BEM.
