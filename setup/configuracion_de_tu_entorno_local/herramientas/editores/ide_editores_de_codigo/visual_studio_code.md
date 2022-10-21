# Visual Studio Code

Poco a poco Visual Studio Code ha superado a Sublime como el editor de texto más usado en Platanus.

## Extensiones

### Relacionadas al stack

* [Ruby](https://marketplace.visualstudio.com/items?itemName=rebornix.Ruby)

* [Ruby Solargraph](https://marketplace.visualstudio.com/items?itemName=castwide.solargraph)

* Vue: qué extensión usar depende de la versión de Vue del proyecto. Activa una de estas dos:

    * [Vetur](https://marketplace.visualstudio.com/items?itemName=octref.vetur) para proyectos en Vue 2

    * [Volar](https://marketplace.visualstudio.com/items?itemName=vue.volar) para Vue 3

* [Jest](https://marketplace.visualstudio.com/items?itemName=Orta.vscode-jest)

* [MJML](https://marketplace.visualstudio.com/items?itemName=attilabuti.vscode-mjml)

* [EditorConfig](https://marketplace.visualstudio.com/items?itemName=EditorConfig.EditorConfig): los proyectos vienen con un `.editorconfig` que nos ayuda a tener algunas configuraciones básicas consistentes. Este plugin se encarga de leerlo y aplicar esas configuraciones a tu editor

* [Tailwind Intellisense](https://marketplace.visualstudio.com/items?itemName=bradlc.vscode-tailwindcss)

* [Tailwind Docs](https://marketplace.visualstudio.com/items?itemName=austenc.tailwind-docs)

* [Headwind](https://marketplace.visualstudio.com/items?itemName=heybourn.headwind) (*Deprecada*): Estandariza el orden de las clases de Tailwind al guardar. Está desactualizada, no incluye todas las clases de las versiones actuales de tailwind. Para proyectos que tienen el plugin de tailwind de eslint, puedes usar el autocorrect, [explicado en la sección de linters](https://www.notion.so/linters.md#vscode)

    > Por defecto Headwind no detecta las clases que se agregan a helpers de rails en un .erb usando class:. Para permitir esto se puede editar la configuración de la extensión en el settings.json de VSCode y poner el siguiente regex en la sección de html:

        `\\\\bclass\\\\s*[=:]\\\\s*[\\\\\\"\\\\']([_a-zA-Z0-9\\\\s\\\\-\\\\:\\\\/]+)[\\\\\\"\\\\']`

### Auxiliares

* [Git Lens](https://marketplace.visualstudio.com/items?itemName=eamodio.gitlens)

* [Trailing Spaces](https://marketplace.visualstudio.com/items?itemName=shardulm94.trailing-spaces)

* [Project Manager](https://marketplace.visualstudio.com/items?itemName=alefragnani.project-manager)

* [Liveshare](https://marketplace.visualstudio.com/items?itemName=MS-vsliveshare.vsliveshare-pack)

* [Error Lens](https://marketplace.visualstudio.com/items?itemName=usernamehw.errorlens)

* [Highlight Matching Tag](https://marketplace.visualstudio.com/items?itemName=vincaslt.highlight-matching-tag)

* [Color Highlight](https://marketplace.visualstudio.com/items?itemName=naumovs.color-highlight)

* [DotENV](https://marketplace.visualstudio.com/items?itemName=mikestead.dotenv)

* [file-icons](https://marketplace.visualstudio.com/items?itemName=file-icons.file-icons)

* [Markdown All in One](https://marketplace.visualstudio.com/items?itemName=yzhang.markdown-all-in-one)

* [Rainbow CSV](https://marketplace.visualstudio.com/items?itemName=mechatroner.rainbow-csv)

* [indent-rainbow](https://marketplace.visualstudio.com/items?itemName=oderwat.indent-rainbow)

* [Open in Github](https://marketplace.visualstudio.com/items?itemName=sysoev.vscode-open-in-github)

* [String Manipulation](https://marketplace.visualstudio.com/items?itemName=marclipovsky.string-manipulation)

Si no lo has hecho todavía, revisa [la sección de linters](https://www.notion.so/linters.md).

### OSX

### Comandos más usados

* `cmd + p`: Busca cualquier archivo del workspace abierto

* `cmd + shift + p`: Busca cualquier comando disponible y lo ejecuta

Para más información, mirar aquí: [https://code.visualstudio.com/docs/getstarted/keybindings](https://code.visualstudio.com/docs/getstarted/keybindings)

### Windows

### WSL 2

Se puede conectar de manera "nativa" a WSL 2, sin tener que preocuparse de paths, compatibilidad de extensiones o performance, usando la extensión [Remote-WSL](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-wsl).

### Linux

TODO
