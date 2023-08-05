# Linters

## Linters

Usamos `rubocop` para Ruby, `eslint` para JS y `stylelint` para CSS. Actualmente incluimos estos linters como dependencias de desarrollo en nuestros proyectos tanto en el `package.json` como el `Gemfile`. Debido a esto, no se necesita instalar nada aparte y basta con correr `yarn install` y `bundle install`.

Además de los linters, cada proyecto incluye archivos con sus reglas.

> En el Gemfile verás que la versión de rubocop está restringida. Hay que tener cuidado al modificarla ya que hasta hace un tiempo no seguían Semantic Versioning y es puede pasar que un cambio en el minor introduzca breaking changes como cambio de nombre de alguna regla que podría estar definida en el proyecto.

### Tips

* Antes de hacer un *commit* recuerda revisar los warnings de los linters

* Deberías fijarte solo en los warnings sobre el código que estás tocando, no es necesario corregir todos los problemas que tenga un archivo antes de agregar una feature en este

* Si se debe arreglar un warning puntual sobre un código de tú rama que ya se *commiteo* una opción es hacer el cambio con un `rebase` o `fixup`, cambiando el commit en el que se introdujo el problema. Esto para dejar la historia más limpia evitando los commits del tipo `style(): fix linter warnings`

### Plugins

Para correr los linters de manera más cómoda, podemos instalar los siguientes plugins:

### VScode

* [Ruby](https://marketplace.visualstudio.com/items?itemName=Shopify.ruby-lsp)

    * Puedes hacer que al guardar un archivo `.rb` se autocorrijan la mayoría de las infracciones. Para que esto funcione, debes incluir lo siguiente en el `settings.json` de tu VSCode (hint: para abrirlo presiona `cmd+shift+p` y busca el comando "Preferences: Open Settings (JSON)):

        ```json
        "[ruby]": {
          "editor.formatOnSave": true,
          "editor.defaultFormatter": "Shopify.ruby-lsp",
        },
        ```

* [ES](https://marketplace.visualstudio.com/items?itemName=dbaeumer.vscode-eslint)

    * Puedes hacer que al guardar un archivo `.js`/ `.vue` se autocorrijan la mayoría de las infracciones, incluyendo el orden de las clases de tailwind si el proyecto incluye el plugin correspondiente. Para que esto funcione, debes incluir lo siguiente en el `settings.json` de tu VSCode (hint: para abrirlo presiona `cmd+shift+p` y busca el comando "Preferences: Open Settings (JSON)):

        ```json
        "editor.codeActionsOnSave": {
          "source.fixAll.eslint": true
        },
        ```

* [stylelint](https://marketplace.visualstudio.com/items?itemName=stylelint.vscode-stylelint)



### Sublime

* [Linter](https://github.com/SublimeLinter/SublimeLinter3)

* [Ruby](https://github.com/SublimeLinter/SublimeLinter-rubocop)

* [ES](https://github.com/roadhump/SublimeLinter-eslint)

* [stylelint](https://github.com/SublimeLinter/SublimeLinter-stylelint)

### Vim

Configurar los linters para que funcionen solo con vim puede ser un poco enredado. Una alternativa es usar VSCode con sus extensiones de linters y [esta extensión de Vim](https://github.com/VSCodeVim/Vim), alcanzando así un punto medio entre la experiencia de uso de un editor más moderno y las *features* de vim.
