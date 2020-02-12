## Gems

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
