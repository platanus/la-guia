## Linters

Para que todos los desarrolladores de Platanus sigamos las mismas reglas de estilo, usamos los siguientes linters:

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
