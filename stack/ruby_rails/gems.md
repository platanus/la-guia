# Gems

Para crear una gema, en vez de usar el generador de `bundler`, podemos utilizar [gemaker](https://github.com/platanus/gemaker) que hace lo mismo pero, además:

* Permite elegir entre dos tipos de gemas: gema de Ruby o engine de Rails.

* Modifica el `README` para adaptarse al estándar de Platanus.

* Agrega en un archivo `CHANGELOG.md` para animarnos a documentar que se hizo en cada versión de la gema.

* Modifica el archivo de licencia nombrando a Platanus en él.

* Agrega la estructura básica para que nuestra gema tenga una CLI.

* Configura el ambiente de test utilizando RSpec.

* Agrega un generador para instalar la gema.

* Configura Circle CI.

```bash
gemaker new my_gem
```

Al correr el comando anterior gemaker, antes de crear la gema, nos hará una serie de preguntas para:

* Dejar listo (o casi listo) archivos como el `README` o el `.gemspec`.

* Que seleccionemos aquella funcionalidad que es opcional y que necesitemos en nuestra gema. Por ej: podríamos no necesitar un CLI o un instalador o querer que nuestra gema sea una extensión de Rails (engine) o no.


