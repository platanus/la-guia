## NodeJS Setup

Para el uso de node utilizamos el manejador de versiones [nodenv](https://github.com/nodenv/nodenv) y algunos plugins.
Siempre usamos la versión LTS, poniendo la `major` en el archivo `.node-version`. Es decir, se debe preferir escribir 10 en vez de 10.12.1.

```bash
# Instalar nodenv y node-build
brew install nodenv node-build

# Agregar tap de nodenv e instalar plugins
brew tap nodenv/nodenv
brew install nodenv-vars nodenv-aliases

# Actualizar las versiones de node disponibles para instalar
brew upgrade node-build
```

> **TIP**: Taps son repositorios de donde brew puede buscar aplicaciones. Brew viene con el *tap* [homebrew-core](https://github.com/Homebrew/homebrew-core) incluido, pero se puede agregar más. En este caso tuvimos que agregar el tap de [nodenv](https://github.com/nodenv/homebrew-nodenv) para poder instalar los plugins de nodenv con brew.

Luego debes cargar nodenv en tu shell para que puedas acceder a las diferentes versiones. Para esto debes agregar la siguiente linea en tu `.bash_profile` o `.zshrc` dependiendo del shell que uses.

```bash
eval "$(nodenv init -)"
```

Nodenv es realmente un clone de rbenv pero para node, por lo que funciona muy parecido. Toda la información de la sección de ruby aplica para node.
