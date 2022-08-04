## Ruby Setup

Para nuestros desarrollos en ruby utilizamos el manejador de versiones [rbenv](https://github.com/rbenv/rbenv) y algunos plugins.

### OSX

#### Previo a la instalación
Antes de empezar con esta instalación tienes que revisar si tienes `rvm` instalado y quitarlo de tu computador.

Para esto ejecuta:
```bash
  rvm
  ```
y si dice que no existe, tu computador está listo para la instalación, y si aparece algo tienes que desinstalarlo con
```bash
  rvm implode

  gem uninstall rvm
  ```
  Finalente, revisa tus archivos `.bash_profile` o `.zshrc` y comprueba que no quedan lineas relacionadas con `rvm`.

#### Instalación
```bash
# Instala rbenv
brew install rbenv

# Instala plugins
brew install rbenv-vars rbenv-aliases rbenv-default-gems
```

Luego debes cargar rbenv en tu shell para que puedas acceder a las diferentes versiones. Para esto debes agregar la siguiente linea en tu `.bash_profile` o `.zshrc` dependiendo del shell que uses. Hay dos formas de hacerlo:

1. Ejecutar el siguiente, que agrega automáticamente la línea necesaria:
  * Si usas `.bash_profile`
  ```bash
  echo 'eval "$(rbenv init -)"' >> ~/.bash_profile
  ```
  * Si usas `.zshrc`
  ```bash
  echo 'eval "$(rbenv init -)"' >> ~/.zshrc
  ```

2. Abrir  `.bash_profile` o `.zshrc` y agregar la linea en el archivo usando el editor de preferencia:

```bash
eval "$(rbenv init -)"
```

Te sugerimos, reiniciar la shell para que se apliquen los cambios. Para comprobar que tenemos `rbenv` instalado correctamente, escribe en tu consola la siguiente linea:

```bash
rbenv
```

Debiera aparecer la versión de rbenv instalada y los comandos que hay para ejecutar.

#### Posibles errores
Un posible error al instalar `rbenv` es no tener bien configurado el PATH de la shell.


#### Instalando versiones de ruby

Para instalar nuevas versiones de ruby puedes usar el plugin [ruby-build](https://github.com/rbenv/ruby-build) o usar los siguientes comandos:

```bash
# Listar todos las versiones de ruby disponibles para instalar
rbenv install --list

# Instalar una version de ruby en particular
rbenv install 2.4.1
```

> **Recomendación**: Define alguna version de ruby que quieras para tener como global haciendo `rbenv global 2.4.1`  De esta manera no estarás usando la version de ruby que trae el sistema operativo. Esto hace que sea más seguro ya que no necesitas usar `sudo` para instalar gemas.

> **Warning**: Si no puedes usar `rbenv` o instalar gemas sin sudo, es muy probable que hayas dado los permisos equivocados en algún paso de la instalación. Esto no es seguro, por lo tanto te recomendamos eliminar todo y volver a hacer los pasos sin dar permisos de super usuario.

> **TIP**: Las versiones de ruby quedan instaladas en `$HOME/.rbenv/versions`

> **TIP**: El paquete `rbenv-default-gems` tiene como objetivo instalar gemas automáticamente cuando instalas una nueva version de ruby. Para esto crea un archivo en `~/.rbenv/default-gems` y agrega línea por línea el nombre de las gemas que quieres que se instalen. Buenos candidatos son `bundler` y `rails`

### Windows

Para instalar `rbenv` con WSL2, sigue las instrucciones de Linux.

### Linux (Ubuntu)

```bash
# Instala rbenv
sudo apt update
sudo apt install rbenv -y
```

Luego debes cargar rbenv en tu shell para que puedas acceder a las diferentes versiones. Para esto debes agregar la siguiente linea en tu `.bashrc` o `.zshrc` (este archivo está normalmente en el directorio `$HOME`) dependiendo del shell que uses.

```bash
eval "$(rbenv init -)"
```

Puedes agregarlo con cualquier editor o hacerlo así (reemplaza `~/.bashrc` por `~/.zshrc` si usas zsh en lugar de bash):

```bash
tee -a ~/.bashrc <<< "eval \"\$(rbenv init -)\""
```

Cierra la ventana del terminal y abre una nueva para que los cambios surjan efecto.

rbenv tiene varios [plugins](https://github.com/rbenv/rbenv/wiki/Plugins). Para instalarlos basta con dejarlos en `~/.rbenv/plugins/`. Recomendamos instalar los siguientes plugins: [`rbenv-vars`](https://github.com/rbenv/rbenv-vars), [`rbenv-aliases`](https://github.com/tpope/rbenv-aliases), [`rbenv-default-gems`](https://github.com/rbenv/rbenv-default-gems):

```bash
# rbenv-vars
git clone https://github.com/rbenv/rbenv-vars.git $(rbenv root)/plugins/rbenv-vars
```
```bash
# rbenv-aliases
git clone https://github.com/tpope/rbenv-aliases.git $(rbenv root)/plugins/rbenv-aliases

rbenv alias --auto
```
```bash
# rbenv-default-gems
git clone https://github.com/rbenv/rbenv-default-gems.git $(rbenv root)/plugins/rbenv-default-gems

```

#### Instalando versiones de ruby

Para instalar nuevas versiones de ruby:

```bash
# Actualizar las versiones de ruby disponibles para instalar
cd $(rbenv root)/plugins/ruby-build && git pull

# Listar todos las versiones disponibles para instalar
rbenv install --list

# Instalar una version en particular
rbenv install 2.7.4
```

> **Recomendación**: Define alguna version de ruby que quieras para tener como global haciendo `rbenv global 2.7.4`  De esta manera no estarás usando la version de ruby que trae el sistema operativo. Esto hace que sea más seguro ya que no necesitas usar `sudo` para instalar gemas.

> **TIP**: Las versiones de ruby quedan instaladas en `~/.rbenv/versions`.

> **TIP**: El plugin `rbenv-default-gems` tiene como objetivo instalar gemas automáticamente cuando instalas una nueva version de ruby. Para esto crea un archivo en `~/.rbenv/default-gems` y agrega línea por línea el nombre de las gemas que quieres que se instalen. Buenos candidatos son `bundler` y `rails`.

### Ruby Aliases

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
