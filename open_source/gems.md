# Gems

Para crear una gema podemos partir con el generador de bundler

```
bundle gem GEM_NAME
```

Con esto se va a generar un `Rakefile` que nos va a dar algunas tasks para simplificarnos la vida.

- rake install
- rake build
- rake relase

#### Spec `gemname.gemspec`

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

## Publicación

Las gemas deben ser publicadas en [rubygems.org](https://rubygems.org). Usamos la linea de comando para esto. La primera vez nos va a pedir nuestras credenciales de rubygems

```bash
# This will tag, push, publish
rake release
```

> **IMPORTANTE:** Las publicaremos con nuestro usuario personal pero debemos agregar como owner al usuario de [platanus](https://rubygems.org/profiles/platanus).

```bash
# Agregar un owner a una gem
gem owner GEM_NAME --add rubygems@platan.us
```

### Publicación automágica

Ahora que tenemos la primera versión arriba, vamos a delegar la responsabilidad a travis que haga el deploy cada vez que hagamos un tag de una nueva versión.

Para esto necesitamos el cli de travis.

```
brew install travis
```

Y luego configuramos un deploy a rubygems contestando las preguntas.

```bash
travis setup rubygems

# Output
Detected repository as platanus/bank-api-gem, is this correct? |yes|
Gem name: |bank-api-gem| bank-api
Release only tagged commits? |yes|
Release only from platanus/bank-api-gem? |yes|
Encrypt API key? |yes|
```

Esto genera unos cambios en el archivo `.travis.yml` que incluyen un token encryptado de la cuenta rubygems con la que estas logeado.
Haz un commit y un pull request con los cambios y ya está.

Happy deploying.

## Un listado de librerías Platanus

{% packagesTable %}{% endpackagesTable %}
  {% rubyGem 'activeadmin_addons' %}{% endrubyGem %}
  {% rubyGem 'admin_invitable', github='platanus/admin-invitable' %}{% endrubyGem %}
  {% rubyGem 'cangrejo', github='platanus/cangrejo-gem'%}{% endrubyGem %}
  {% rubyGem 'paperclip_upload' %}{% endrubyGem %}
  {% rubyGem 'pincers' %}{% endrubyGem %}
  {% rubyGem 'potassium' %}{% endrubyGem %}
  {% rubyGem 'punto_pagos_rails' %}{% endrubyGem %}
  {% rubyGem 'push_to_sns' %}{% endrubyGem %}
  {% rubyGem 'rails_pallet' %}{% endrubyGem %}
{% finishPackagesTable %}{% endfinishPackagesTable %}
