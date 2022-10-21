# Ruby Gems

Las gemas deben ser publicadas en [rubygems.org](https://rubygems.org/). Las publicaremos con nuestro usuario personal pero debemos agregar como owner al usuario de [platanus](https://rubygems.org/profiles/platanus).

```bash
# Agregar un owner a una gem
gem owner GEM_NAME --add rubygems@platan.us
```

## Configuración en Circle CI

Para hacer el deploy usando CircleCI, necesitamos agregar el archivo de configuración en la gema. Puedes ver un ejemplo de configuración [aquí](https://github.com/platanus/power_api/pull/29), pero en resumen lo que se hizo fue:

### Agregar el script en `.circleci/setup-rubygems.sh`

```plain text
mkdir ~/.gem
echo -e "---\\r\\n:rubygems_api_key: $RUBYGEMS_API_KEY" > ~/.gem/credentials
chmod 0600 /home/circleci/.gem/credentials
```

Para poder utilizar la api key de rubygems al hacer el deploy.

### Agregar el job "deploy" en `.circleci/config.yml`

```yaml
deploy:
  executor: main-executor
    steps:
      - setup
      - run:
          name: Setup rubygems
          command: bash .circleci/setup-rubygems.sh
      - run:
          name: Publish to rubygems
          command: |
            gem build power_api.gemspec
            version_tag=$(git describe --tags)
            gem push power_api-${version_tag#v}.gem
```

Esto:

* Ejecuta el setup básico del proyecto.

* Ejecuta el script para copiar la api key en un archivo que se utilizará para el deploy de la gema.

* Crea el archivo .gem

* Crea la nueva versión en rubygems.

### Agregar el job deploy al workflow

```yaml
workflows:
  version: 2
  main:
    jobs:
      - lint:
          context: org-global
      - test:
          matrix:
            parameters:
              ruby-version: ["2.6", "2.7"]
      - deploy:
          context: org-global
          filters:
            tags:
              only: /.*/
            branches:
              ignore: /.*/
```

Lo que se hace aquí es ejecutar el job deploy siempre que se cree un nuevo tag en github.

> org-global es un contexto que tiene definido la variable de entorno `RUBYGEMS_API_KEY` que necesitamos para hacer el deploy.

### Publicación

Una vez configurado CircleCI en la gema tenemos que:

1. Cambiar `VERSION` en `lib/my_new_gem/version.rb` para que apunte a la nueva verisón.

1. Cambiar el título `Unreleased` a la versión nueva en el `CHANGELOG.md`.

1. Correr `bundle install`.

1. Hacer commit (directo en master) de un nuevo release. Por ejemplo: `Releasing v0.1.0`.

1. Crear el tag. Por ejemplo: `git tag v0.1.0`.

1. Hacer push del tag. Por ejemplo: `git push origin v0.1.0`.

¡Listo!
