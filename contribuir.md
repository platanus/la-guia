## Contribuir

La idea es que todos los que formamos Platanus podamos participar en la evolución constante de esta guía. Para hacerlo de forma ordenada y que sea útil sigue estos lineamientos:

### 1. Identifica a qué sección de la guía corresponde lo que quieres agregar.

Las secciones son:

* [Cultura](cultura/README.md)
* [Stack](stack/README.md)
* [Setup](setup/README.md)
* [Herramientas](tools/README.md)
* [Deployment](deployment/README.md)

Por ejemplo: si te volviste experto en el manejo de [sidekiq](https://github.com/mperham/sidekiq) (herramienta que usamos para el procesamiento en background) deberías agregar este conocimiento a [Stack/Ruby](stack/ruby/README.md)

### 2. Si lo que agregas es solo un link, agregalo directamente en la sección que corresponda.

Siguendo con el ejemplo, en [Stack/Ruby](stack/ruby/README.md) deberías agregar:

`* [Sidekiq](https://github.com/mperham/sidekiq): lo utilizamos para procesamiento en background.`

### 3. Si lo que agregas es más que un link, crea una sección nueva.

Siguendo con el ejemplo, deberías crear dentro del directorio `/stack/ruby` un archivo `/sidekiq.md` y apuntarlo desde [Stack/Ruby](stack/ruby/README.md) así:

`* [Sidekiq](sidekiq.md)`

y dentro de `sidekiq.md` deberías tener algo como:

```
# Sidekiq

En Platanus utilizamos [sidekiq](https://github.com/mperham/sidekiq) para procesamiento en background.

### Contenido extra 1

bla bla

### Contenido extra 2

ble ble
```

### 4. Si lo que agregas es demasiado grande para mantenerlo en una única sección, utiliza directorios.

Siguendo con el ejemplo, deberías tener en [Stack/Ruby](stack/ruby/README.md) un directorio `sidekiq` con un archivo `README.md` y apuntarlo  así:

`* [Sidekiq](sidekiq/README.md)`

> Es importante que en archivo se llame `README.md` para que github lo levante automáticamente al entrar a la carpeta.

Luego deberías agregar cada sección dentro del directorio `/sidekiq` y apuntarlas en `sidekiq/README.md` así:

```
# Sidekiq

* [Contenido extra 1](extra1.md)
* [Contenido extra 2](extra2.md)
```

y por último, las secciones quedarían así:

```
### Contenido extra 1

bla bla
```

y

```
### Contenido extra 2

ble ble
```

### 5. Respeta el tamaño de los títulos.

* `## Título`
* `### Subtítulo`
* `#### Sub Subtítulo`

### 6. Asegúrate que el contenido a agregar sea valioso.

En general deberías:

 * Agregar links a herramientas que utilizamos en Platanus.
 * Evitar copy/paste de READMEs de librerias y gemas. Con el link basta.
 * Evitar agregar conentido fácilmente deprecable.
 * Agregar contenido extra cuando este explique alguna manera particular de usar una herramienta en Platanus. Por ejemplo, si configuramos algo específico de sidekiq en nuestros proyectos, quizás valga la pena mencionarlo pero si es algo que sale en el README de la gema, mejor no hacerlo.
 * Siempre que sea posible, escribir en el blog antes que en la guía y luego hacer el link al post.

### 7. Recuerda agregar los links a [summary](SUMMARY.md)

Es necesario colocar en [summary](SUMMARY.md) referencias al contenido ya que a partir de este archivo, se generará la guía [aquí](https://la-guia.platan.us/)
