Git
===

### Commits en Git

Los mensajes de commit:

  * siempre los hacemos en inglés
  * normalmente tienen una sola línea (aunque sabemos que más podría ser mejor)
  * la primera línea se forma de un **tipo**, un **contexto** y una **descripción**
    ```
    tipo(contexto):descripción
    ```

  La línea no debiera tener más de 100 caracteres para que se lea bien en Github.

#### Tipo

El tipo nos ayuda a clasificar los commits. Los tipos que usamos son:

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

#### Descripción

  * usamos el verbo imperativo en inglés:  "change" not "changed" nor "changes"
  * sin mayúscula al principio
  * sin punto (.) al final

Nota: Esto es un extracto/traducción de [este documento](https://github.com/angular/angular.js/blob/master/DEVELOPERS.md#commits), que es más completo, pero que por el momento no es una práctica que sigamos completamente en Platanus

### Branches y Pull-requests

El flujo normal de desarrollo es crear un nuevo branch y luego hacer un PR a master.

A veces, si el feature es muy grande, conviene tener un feature branch, y primero hacer PRs a ese branch antes del gran PR a master.


