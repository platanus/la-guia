# Gems

Para crear una gema, en vez de usar el generador de bundler, podemos utilizar [gemaker](https://github.com/platanus/gemaker) que hace lo mismo pero, además:

- permite elegir entre dos tipos de gemas: gema de Ruby o engine de Rails.
- modifica el `README` para adaptarse al estándar de Platanus.
- agrega en un archivo `CHANGELOG.md` para animarnos a documentar que se hizo en cada versión de la gema.
- modifica el archivo de licencia nombrando a Platanus en él.
- agrega la estructura básica para que nuestra gema tenga una CLI.
- configura el ambiente de test utilizando Rspec.
- agrega un generador para instalar la gema.
- configura Hound y Rubocop en la propia gema.
- configura Travis.

```
gemaker new my_gem
```

Al correr el comando anterior gemaker, antes de crear la gema, nos hará una serie de preguntas para:

- dejar listo (o casi listo) archivos como el `README` o el `.gemspec`.
- que seleccionemos aquella funcionalidad que es opcional y que necesitemos en nuestra gema. Por ej: podríamos no necesitar un CLI o un instalador o querer que nuestra gema sea una extensión de Rails (engine) o no.


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
