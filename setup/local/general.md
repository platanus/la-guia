## Setup General

Sabemos que empezar en con un nuevo equipo ya es difícil, hay muchas cosas que hay que hacer y mucho que ajustar. Por eso, para facilitar el proceso de preparar el ambiente de desarrollo en tu flamante mac, tenemos un sistema que hará por ti parte del proceso.

Anda a https://strap.platan.us e ingresa con tu usuario de github.

Sigue los paso que ahí se muestran y anda prepararte un café y a conocer con alguno de tus nuevos compañeros de trabajo.

> **TIP**: Si en tu usuario de github tienes un repo con el nombre `dotfiles`, strap clonará ese repositorio en `$HOME/.dotfiles` y ejecutará el archivo `scripts/setup` para instalar los dotfiles en tu sistema

> **TIP**: Si tienes un archivo `$HOME/.Brewfile` en un computador, el script instalara todo lo que este definido ahí.

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

- Elasticsearch 6.2.4 `brew install elasticsearch@6.2.4`

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
