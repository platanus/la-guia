# Git

Utilizamos [git](https://git-scm.com/) para el control de versiones junto con [github](https://github.com/platanus)

## Commits

Los mensajes de commit:

* siempre los hacemos en inglés

* normalmente tienen una sola línea (aunque sabemos que más podría ser mejor)

* la primera línea se forma de un **tipo**, un **contexto** y una **descripción**

    ```plain text
    tipo(contexto): descripción
    ```

La línea no debiera tener más de 100 caracteres para que se lea bien en Github.

## Tipo

El tipo nos ayuda a clasificar los commits. Los tipos que usamos son:

* **feat**: Un nuevo feature

* **fix**: La corrección de un bug

* **docs**: Cambios en la documentación

* **style**: Cambios que no afectan el significado del código (espacios, indentación, etc.)

* **refactor**: Un cambio en el código que no agrega una funcionalidad ni corrige un bug

* **perf** Cambios en el código que sólo mejoran la performance

* **test**: Agrega, corrige o mejora tests

* **chore**: Cambios al proceso de build y herramientas auxiliares

## Contexto

El contexto es una palabra que hace referencia al lugar del código o funcionalidad que afecta el commit. Debe escribirse usando `kebab-case`, por ejemplo: `user-signup`

De manera opcional, se puede agregar información sobre el componente específico del código afectado. Si se agrega esta información:

* Debe ir después del contexto, separado usando un slash (`/`). Por ejemplo: `api/LoginService`

* El nombre del componente modificado debe estar en el mismo formato en el que aparece en el código (por ejemplo en `CamelCase` si es una clase ruby).

## Descripción

* usamos el verbo imperativo en inglés: "change" no "changed" ni "changes"

* separado por un espacio del contexto

* sin mayúscula al principio

* sin punto (.) al final

Nota: Esto es un extracto/traducción de [este documento](https://github.com/angular/angular.js/blob/master/DEVELOPERS.md#commits), que es más completo, pero que por el momento no es una práctica que sigamos completamente en Platanus

## Branches y Pull-requests

Los features los hacemos en un nuevo branch y hacemos un pull-request hacia master.

### Ejemplo

Como ejemplo de estas recomendaciones el siguiente commit soluciona un bug en el componente/clase `SignUpForm` del frontend de una aplicación, en el cual no se estaba entregando feedback de la validación sobre si el nombre de usuario estaba disponible o no:

```plain text
fix(ui/SignUpForm): validate username availability

The `SignUpForm` component wasn´t validating the availability of the `username` field and only displayed a `couldn´t create account` error on submit.

This commit adds an asynchronous validation when typing on the field so the user will know if the username is available before completing further fields.
```

## Rebase

Para ordenar un poco los commits, usamos fixups y rebase. [En este post de nuestro blog](https://plata.news/blog/manteniendo-la-historia-limpia-usando-git-rebase/) puedes leer sobre rebase y cómo lo usamos para mantener la historia limpia. También revisa [este post](https://fle.github.io/git-tip-keep-your-branch-clean-with-fixup-and-autosquash.html) sobre fixup, otra herramienta del rebase que usamos para esto.


