# Ruby

## Version Management

La version actual que usamos se puede ver en [ruby.platan.us/latest](http://ruby.platan.us/latest)

Para administrar las versiones de ruby usamos [rbnev](https://github.com/sstephenson/rbenv)

Los proyectos deben tener la version de ruby definida en el archivo
`.ruby-version` en la raíz del proyecto. Debemos definir versiones
sin incluir el patch. Es decir `2.1` para usar cualquier version `2.1.x`.

Esta version viene definida si usamos
[potassium](http://github.com/platanus/potassium) para inicializar
nuestro proyecto.

También podemos defirla usando

```
rbenv local <alias-version>
```

### Aliases

Para esto debemos tener definidos aliases para las principales versiones:

```shell
$ ls -l /opt/rubies
2.1.6
2.1 -> /opt/rubies/2.1.6 #symlink a la version mayor
2.2.1
2.2.3
2.2 -> /opt/rubies/2.2.3 #symlink a la version mayor
```

## Gems

Para crear una gema podemos partir con el generador de bundler

```
bundle gem GEM_NAME
```

Con esto se va a generar un `Rakefile` que nos va a dar algunas tasks para simplificarnos la vida.

- rake install
- rake build
- rake relase

#### Spec

En el gemspec debemos llenar al menos los siguientes campos

```ruby
#negroku gemspec example
spec = Gem::Specification.new do |s|
  s.name = 'negroku'
  s.version = Negroku::VERSION
  s.authors = ['Platanus', 'Juan Ignacio Donoso']
  s.emails = ['rubygems@platan.us', 'juan.ignacio@platan.us']
  s.homepage = 'http://github.com/platanus/negroku'
  s.summary = 'Capistrano recipes collection'
  s.description = 'Deploy applications right out of the box using nginx, unicorn, bower, rails, etc'
...
```

#### Publicación

Las gemas deben ser publicadas en [rubygems.org](https://rubygems.org). Usamos la linea de comando para esto. La primera vez nos va a pedir nuestras credenciales de rubygems

```bash
# This is like tag, push, publish
rake release
```

**IMPORTANTE:** Las publicaremos con nuestro usuario personal pero debemos agregar como owner al usuario de [platanus](https://rubygems.org/profiles/platanus).

```bash
# Agregar un owner a una gem
gem owner GEM_NAME --add rubygems@platan.us
```

{% getPostsByTag 'ruby' %}{% endgetPostsByTag %}
