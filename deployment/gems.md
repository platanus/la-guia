## Ruby Gems

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
