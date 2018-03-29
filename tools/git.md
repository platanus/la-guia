Git
===

### Commits en Git

Los mensajes de commit:

  * siempre los hacemos en inglés
  * normalmente tienen una sola línea (aunque sabemos que más podría ser mejor)
  * la primera línea se forma de un **tipo**, un **contexto** y una **descripción**
    ```
    tipo(contexto): descripción
    ```

  La línea no debiera tener más de 100 caracteres para que se lea bien en Github.

#### Tipo

  * **feat**: Un nuevo feature
  * **fix**: La corrección de un bug
  * **docs**: Cambios en la documentación
  * **style**: Cambios que no afectan el significado del código (espacios, indentación, etc.)
  * **refactor**: Un cambio en el código que no agrega una funcionalidad ni corrige un bug
  * **perf** Cambios en el código que sólo mejoran la performance
  * **test**: Agrega, corrige o mejora tests
  * **chore**: Cambios al proceso de build y herramientas auxiliares

#### Contexto

El contexto es una palabra que haga referencia al lugar del código donde afecta el commit.

  * Puede contener información adicional sobre el componente del código modificado, en cuyo caso debe ir al inicio y separado usando un slash (`/`).
  * El nombre del componente modificado debe estar en el mismo formato `kebab-case`.

#### Descripción

  * usamos el verbo imperativo en inglés:  "change" not "changed" nor "changes"
  * sin mayúscula al principio
  * sin punto (.) al final
  * en caso que se requiera agregar más texto puede hacerse como texto en formato `markdown` separado por una línea en blanco del mensaje principal

Nota: Esto es un extracto/traducción de [este documento](https://github.com/angular/angular.js/blob/master/DEVELOPERS.md#commits), que es más completo, pero que por el momento no es una práctica que sigamos completamente en Platanus.

### Branches y Pull-requests

Salvo cosas muy insignificantes, los features los hacemos en un nuevo branch y hacemos un pull-request hacia master.

### Ejemplo

Como ejemplo de estas recomendaciones el siguiente commit soluciona un bug en el componente/clase `SignUpForm` del frontend de una aplicación, en el cual no se estaba entregando feedback de la validación sobre si el nombre de usuario estaba disponible o no:

```
fix(ui/sign-up-form): validate username availability

The `SignUpForm` component wasn´t validating the availability of the `username` field and only displayed a `couldn´t create account` error on submit.

This commit adds an asynchronous validation when typing on the field so the user will know if the username is available before completing further fields.
```
