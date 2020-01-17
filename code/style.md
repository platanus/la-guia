# Guía de Estilo

Con el fin de escribir código elegante, consistente y fácil de entender por todos los miembros (y no miembros) de Platanus a lo largo y ancho de todos nuestros proyectos, es que decidimos utilizar reglas de estilo para cada uno de los lenguajes que manejamos en Platanus. Para lograr este objetivo, utilizamos:

### Una versión a medida de [Hound](https://github.com/platanus/hound)

Esta nos permite, mantener por cada lenguaje, un conjunto de reglas que se aplicarán al momento de hacer un Pull Request en Github. Hound o "El Mono" (como decimos en Platanus), comentará nuestro PR señalando violaciones de estilo si es que hubiesen.

#### ¿Cómo funciona?

Al momento de hacer un PR en un proyecto, Hound leerá el archivo de configuración [`.hound.yml`](https://github.com/platanus/la-guia/blob/master/.hound.yml) (que hemos decidido mantener en este repositorio). En este, revisará qué lenguajes están habilitados y, por cada uno, buscará una referencia (bajo el key `config_file`) a las [reglas de estilo](https://github.com/platanus/la-guia/tree/master/style/config) que usamos en Platanus para ese lenguaje. Con las reglas en mano, el mono aplicará las mismas contra los archivos del PR y generará comentarios si se produjeron violaciones de estilo en el código.

#### ¿Cómo agrego un repositorio?

Para que hound empieze a revisar los PR de un repositorio, debes ingresar a http://monkeyci.platan.us,
entrar con tu cuenta de github y activar el repositorio.

### Una [Ruby CLI](https://github.com/platanus/hound-cli)

Esta nos permite, localmente, simular lo mismo que ocurre con Hound. Esto lo hacemos con el objetivo de poder corregir localmente violaciones de estilo antes que "El Mono" nos reclame en el PR.

#### ¿Cómo funciona?

Luego de instalar la CLI, el correr el comando `hound rules update` buscará, del mismo modo que hace Hound, las reglas de estilo para los linters habilitados en [`.hound.yml`](https://github.com/platanus/la-guia/blob/master/.hound.yml). La diferencia es que localmente, se generarán archivos en nuestro `$HOME` path que usaremos con los distintos linters instalados en nuestro sistema.

## Instalación

### CLI

```sh
# hound-cli
gem install hound-cli
```

### Linters

Para poder ejecutar la revisión de reglas localmente es que necesitamos de los linters de cada lenguaje.

```sh
# ruby
gem install rubocop -v '0.65.0'
gem install rubocop-rspec -v '1.35.0'

# js
npm install -g eslint
npm install -g eslint-plugin-import
npm install -g eslint-plugin-vue

# typescript
npm install -g tslint typescript

# stylelint (css, scss)
# Solo Sublime Text requiere que esté instalado
npm install -g stylelint
```

### Plugins

Para correr los linters de manera más cómoda, podemos instalar los siguientes plugins:

#### Atom

* [Linter](https://atom.io/packages/linter)
* [Ruby](https://atom.io/packages/linter-rubocop)
* [ES](https://atom.io/packages/linter-eslint)
* [TypeScript](https://atom.io/packages/linter-tslint)
* [stylelint](https://atom.io/packages/linter-stylelint)

#### VScode

* [Ruby](https://github.com/misogi/vscode-ruby-rubocop)
* [ES](https://marketplace.visualstudio.com/items?itemName=dbaeumer.vscode-eslint)
* [TypeScript](https://marketplace.visualstudio.com/items?itemName=eg2.tslint)
* [stylelint](https://marketplace.visualstudio.com/items?itemName=shinnn.stylelint)

Para que funcione Rubocop, se debe agregar la siguiente configuración en los Settings:

```
"ruby.rubocop.executePath": "/Users/TU_USUARIO/.rbenv/shims/"
```

#### Sublime

* [Linter](https://github.com/SublimeLinter/SublimeLinter3)
* [Ruby](https://github.com/SublimeLinter/SublimeLinter-rubocop)
* [ES](https://github.com/roadhump/SublimeLinter-eslint)
* [TypeScript](https://github.com/lavrton/SublimeLinter-contrib-tslint)
* [stylelint](https://github.com/kungfusheep/SublimeLinter-contrib-stylelint)

#### RubyMine

* [Ruby](https://plugins.jetbrains.com/plugin/7604?pr=)
* [ES](https://plugins.jetbrains.com/plugin/7494?pr=)
* [stylelint](https://www.jetbrains.com/help/ruby/2017.1/using-stylelint-code-quality-tool.html)

## Contribuir

Si crees que alguna regla puede mejorar o no debe estar habilitada puedes proponer un cambio siguiendo los pasos:

1. Modificar el archivo de estilo que contenga la regla que quieres modificar. Como señalamos anteriormente, las reglas se encuentran dentro del directorio [`/style/config`](https://github.com/platanus/la-guia/tree/master/style/config) en este mismo repositorio.
2. Hacer un Pull Request explicando la razón.
3. Luego de que el cambio es aceptado, cada usuario deberá correr localmente `hound rules update` para maneter sincronizadas las reglas locales y remotas.
