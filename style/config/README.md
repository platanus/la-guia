## Cómo actualizar `.rubocop.yml`

Ni idea por qué los artistas detrás de estas [rubocop](https://github.com/rubocop-hq/rubocop) no siguen SEMVER y en cada cambio de versión minor se rompe todo... en fin.

Para actualizar `.rubocop.yml` se usa [mry](https://github.com/pocke/mry) que afortunadamente hace todo más fácil. En la carpeta donde está el `.rubocop.yml` que quieres actualizar (`style/config` en el repo de las reglas)

 - Asegúrate de tener instalado `mry`: `gem install mry`
 - Correr `mry` especificando la versión actual y a la que se quieren actualizar las reglas... con esto, además de actualizar los cops existentes se agregan las reglas nuevas que se agregaron entre las dos versiones. Por ejemplo: `mry --from=0.61.1 --target=0.65 .rubocop.yml`