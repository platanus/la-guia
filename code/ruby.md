# Ruby

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
