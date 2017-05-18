# Ambiente de desarrollo

En este articulo se enumeran las principales herramientas que usamos en Platanus. La idea es que aquí encuentres los pasos iniciales para tener tu computador en perfecto estado para codear.
Ademas encontrarás algunas recomendaciones particulares de como instalar estas herramientas.

> **Recomendación**: macOS es la plataforma recomendada para trabajar en Platanus.

## tl;dr

Sabemos que empezar en con un nuevo equipo ya es difícil, hay muchas cosas que hay que hacer y mucho que ajustar. Por eso, para facilitar el proceso de preparar el ambiente de desarrollo en tu flamante mac, tenemos un sistema que hará por ti parte del proceso.

Anda a https://strap.platan.us e ingresa con tu usuario de github.

Sigue los paso que ahí se muestran y anda prepararte un café y a conocer con alguno de tus nuevos compañeros de trabajo.

Igual es buena idea continuar leyendo el articulo, ya que solo las cosas descritas en la sección [.base()](#base) están automatizado con el script.

> **TIP**: Si en tu usuario de github tienes un repo con el nombre `dotfiles`, strap clonará ese repositorio en `$HOME/.dotfiles` y ejecutará el archivo `scripts/setup` para instalar los dotfiles en tu sistema

> **TIP**: Si tienes un archivo `$HOME/.Brewfile` en un computador, el script instalara todo lo que este definido ahí. [más detalles](#brew-bundle)

> **TIP:** Si no eres nuevo en Platanus y estas usando el deprecado y nunca bien ponderado boxen, puedes ejecutar `cd ~/src/our-boxen && script/nuke --all --force` para eliminar todo instalado por boxen y empezar de "cero"


## El paso a paso

### Instalación base

Las siguientes son algunas cosas que puedes hacer e instalar para tener una base sólida y segura en la cual poder instalar todas las herramientas necesarias.

- Habilita el [FileVault](https://support.apple.com/en-us/HT204837) para encriptar los datos de tu disco duro.

- Instala los [Xcode Command Line Tools](http://railsapps.github.io/xcode-command-line-tools.html)

- Configura Git para que se pueda autenticar con GitHub y para tener permisos en los repositorios de Platanus.

  - Configura tu username en Git para que los commits tengan el autor y commiter correcto [Github Guide](https://help.github.com/articles/setting-your-username-in-git/)

  - Configura Git para que guarde en el Keychain tu password, de esta manera no te lo preguntará cada vez que haces pull o push. [Github Guide](https://help.github.com/articles/caching-your-github-password-in-git/)

  > **TIP**: Siempre [clona los repositorios usando https](https://help.github.com/articles/which-remote-url-should-i-use/#cloning-with-https-urls-recommended) y no ssh. De esta manera usaras el puerto 80 que esta abierto en todas partes, me ha pasado mas de una vez que en cafés o coworks esta cerrado el puerto 22.

  > **TIP**: En vez de guardar el password en el Keychain, usa un personal token de tu usuario. De esta manera, aunque tengas habilitado el 2FA en GitHub, podrás acceder a tus repositorio de manera transparente.

- Instala Homebrew como tu package manager. [https://brew.sh/](https://brew.sh/)

- Instala los updates de sistema operativo que estén disponibles.

  > **Recomendación**: Instala la última version del sistema operativo macOS.

### Servicios

Normalmente para desarrollar usamos algunos servicios, como bases de datos, cache, etc. Estos son algunos ejemplos de los servicios que más usamos en Platanus.

- PostgresSQL 9.6.x con Postgis `brew install postgis`

- MYSQL 5.7.x `brew install mysql`

- Redis 3 `brew install redis`

- Elasticsearch 2.4 `brew install elasticsearch@2.4`

Una vez instalados los servicios pueden ser iniciados o detenidos usando la extensión services de brew.

```bash
# Lista los servicios instalados
brew services list

# Inicia un servicio sin que se inicie automáticamente al iniciar el computador
brew services run <nombre-servicio>

# Inicia un servicio y que se inicie automáticamente al iniciar el computador
brew services start <nombre-servicio>

# Detiene un servicio
brew services stop <nombre-servicio>
```

> **TIP**: Puedes usar la herramienta BitBar para tener una vista gráfica de que servicios están instalados y cuales están corriendo [BitBar](https://github.com/matryer/bitbar), [BitBar Homebrew Services](https://github.com/matryer/bitbar-plugins/blob/master/Dev/Homebrew/brew-services.10m.rb)

> **TIP**: Una alternativa para los servicios es usar docker y correr los diferentes servicios en containers. Aquí puedes usar [Captain](https://getcaptain.co/) si te gustó la idea de tener tu servicios a la mano en el menú.

### Ruby

Para nuestros desarrollos en ruby utilizamos el manejador de versiones [rbenv](https://github.com/rbenv/rbenv) y algunos plugins.

```bash
# Instala rbenv y ruby-build
brew install rbenv ruby-build

# Instala plugins
brew install rbenv-vars rbenv-aliases rbenv-default-gems
```

Luego debes cargar rbenv en tu shell para que puedas acceder a las diferentes versiones. Para esto debes agregar la siguiente linea en tu `.bash_profile` o `.zshrc` dependiendo del shell que uses.

```bash
eval "$(rbenv init -)"
```

#### Instalando versiones de ruby

Para instalar nuevas versiones de ruby puedes usar el plugin [ruby-build](https://github.com/rbenv/ruby-build)

```bash
# Listar todos las versiones disponibles para instalar
rbenv install --list

# Instalar una version en particular
rbenv install 2.4.1

# Actualizar las versiones de ruby disponibles para instalar
brew upgrade ruby-build
```

> **Recomendación**: Define alguna version de ruby que quieras para tener como global haciendo `rbenv global 2.4.1`  De esta manera no estarás usando la version de ruby que trae el sistema operativo. Esto hace que sea más seguro ya que no necesitas usar `sudo` para instalar gemas.

> **TIP**: Las versiones de ruby quedan instaladas en `$HOME/.rbenv/versions`

> **TIP**: El paquete `rbenv-default-gems` tiene como objetivo instalar gemas automáticamente cuando instalas una nueva version de ruby. Para esto crea un archivo en `~/.rbenv/default-gems` y agrega línea por línea el nombre de las gemas que quieres que se instalen. Buenos candidatos son `bundler` y `rails`

#### Ruby Aliases

Como estandar en Platanus usamos aliases para definir las versiones de ruby que utilizan los proyectos. De esta manera nos evitamos tener que actualizar los proyectos cada vez que instalas una nueva version de ruby. Esto nos ayuda en menos manteción y en menos uso de espacio en disco.

Los aliases son simplemente symbolic links de una version de ruby con un nombre en particular. Estos aliases podrían ser nombre arbitrarios, pero nosotros usamos la version de ruby sin el `patch`

Para manejar estos aliases, puedes usar el plugin de [rbenv-aliases](https://github.com/tpope/rbenv-aliases) que crea los alias automáticamente al instalar nuevas versiones de ruby.

```bash
# Listar los aliases
rbenv alias

2.2 => 2.2.7
2.3 => 2.3.4
2.4 => 2.4.1
```

### NodeJS

Para el uso de node utilizamos el manejador de versiones [nodenv](https://github.com/nodenv/nodenv) y algunos plugins.

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

#### Instalando versiones de node

Ver sección [Instalando Versiones](#instalando-versiones-de-ruby) de ruby

#### Node Aliases

Ver sección [Aliases](#ruby-aliases) de ruby

### Homebrew

En Platanus utilizamos otras herramientas, algunas para ejecutar en el terminal y otras aplicaciones de escritorio. Además seguro que tu tienes tus preferencias en aplicaciones con las que te gusta trabajar. (Spotify, iTerm2 ?

#### `brew install`

Como ya vimos antes, las herramientas para el terminal las podemos instalar con *brew*. Por ejemplo:

```bash
brew install git heroku imagemagick jq yarn
```

#### `brew cask install`

También puedes instalar aplicaciones de escritorio con este comando especial de **brew**

```bash
brew cask install 1password captain docker google-chrome \
  screenhero slack harvest iterm2
```

#### `brew bundle`

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
cask 'screenhero'
cask 'slack'
cask 'harvest'
cask 'iterm2'
```

Luego ejecutar el siguiente comando para instalar las aplicaciones.

```bash
brew bundle --global
```
