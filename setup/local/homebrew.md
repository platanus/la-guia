## Homebrew

En Platanus utilizamos otras herramientas, algunas para ejecutar en el terminal y otras aplicaciones de escritorio. Además seguro que tu tienes tus preferencias en aplicaciones con las que te gusta trabajar.

### `brew install`

Como ya vimos antes, las herramientas para el terminal las podemos instalar con *brew*. Por ejemplo:

```bash
brew install git heroku imagemagick jq yarn
```

### `brew cask install`

También puedes instalar aplicaciones de escritorio con este comando especial de **brew**

```bash
brew cask install 1password captain docker google-chrome \
  slack harvest iterm2
```

### `brew bundle`

Para mantener un poco de orden en que herramientas instalas con brew, puedes tener un archivo llamado `.Brewfile` en tu `$HOME` con todas las cosas que quieres instalar. Para las aplicaciones de los ejemplos de arriba seria

```bash
# $HOME/.Brewfile
brew 'git'
brew 'heroku'
brew 'imagemagick'
brew 'jq'
brew 'yarn'

cask '1password'
cask 'captain'
cask 'docker'
cask 'google-chrome'
cask 'slack'
cask 'harvest'
cask 'iterm2'
```

Luego ejecutar el siguiente comando para instalar las aplicaciones.

```bash
brew bundle --global
```
