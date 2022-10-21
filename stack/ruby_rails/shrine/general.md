# General

[Shrine](https://github.com/shrinerb/shrine) es una gema para manejar de manera simple la tarea de subir y adjuntar archivos.

## ¿Por qué la usamos?

Anteriormente usábamos Paperclip, hasta que fue deprecada en favor de la solución incluida en Rails, ActiveStorage. Dado esto, nos cambiamos a ActiveStorage (AS) para no quedarnos con una herramienta sin soporte.

Sin embargo, hay algunos detalles que se manejan mejor en Shrine, por ejemplo:

* Shrine tiene un diseño modular, utilizando `plugins` para las distintas funcionalidades que ofrece. Esto permite cargar solo las que en verdad se usen

* Shrine promueve separación de responsabilidades, introduciendo el concepto de `Uploader`. Estos se encargan de la lógica de subida de un tipo de archivo en particular

* AS no tiene validaciones, por ejemplo de tamaño o tipo de archivo. En Shrine se pueden agregar fácilmente usando el plugin [validation_helpers](https://shrinerb.com/docs/plugins/validation_helpers)

* Recién en Rails 6.1 se está dando soporte a acceso público de archivos, con Shrine se puede definir a nivel de configuración o por `Uploader`

* Con Shrine se pueden setear `default_url` por Uploader, para cuando el archivo es `nil`

## ¿Cómo la usamos?

### Instalación

La gema viene instalada si el proyecto se generó usando [Potassium](https://github.com/platanus/potassium) con la opción `Shrine` elegida como *storage*. También se incluyen un par de uploaders que sirven como base. Si el proyecto se generó sin una opción de storage, se puede agregar corriendo `potassium install file_storage` y seleccionando `Shrine`.

### Ejemplo básico

> Para estos ejemplos se utilizó la versión 3.2.1 de Shrine

Supongamos que tenemos un `Uploader` de imágenes genérico, que podría verse así:

```ruby
class ImageUploader < Shrine
  plugin :validation_helpers

  Attacher.validate do
    validate_mime_type %w[image/jpeg image/jpg image/png image/svg+xml image/gif]
  end
end
```

Aquí tenemos el plugin [validation_helpers](https://shrinerb.com/docs/plugins/validation_helpers), que nos permite usar `validate_mime_type` para verificar que el tipo del archivo corresponda efectivamente a una imagen.

Con esto ya podemos empezar a incluir imágenes en los modelos. Si queremos un `attachment` llamado `photo` habría que agregar la columna `photo_data` a la tabla (tipo `text` o `jsonb`) y agregar lo siguiente en el modelo:

```ruby
include ImageUploader::Attachment(:photo)
```

> El nombre de la columna siempre debe incluir el sufijo _data

### Ejemplo: heredando de un uploader

Ahora, imaginemos que se quiere agregar una imagen de perfil para los usuarios. Podríamos usar el `ImageUploader` definido antes, pero se quieren un par de cosas extra para esta `profile_picture`:

* Límite de peso, 5 MB. Pueden haber muchas imágenes de perfil distintas en una misma vista y no queremos que quede muy pesada

* El usuario puede elegir no tener una foto de perfil

* Comúnmente se usará la imagen en dos tamaños

Como esta sigue siendo una imagen y queremos mantener la validación definida en `ImageUploader`, vamos a definir un nuevo uploader que herede de este:

```ruby
class ProfilePictureUploader < ImageUploader
end
```

**Límite de peso**

Para esto recurrimos nuevamente al `validations_helper`, esta vez usando `validate_max_size`. Para mantener las validaciones de la clase padre, [se debe llamar a ](https://shrinerb.com/docs/plugins/validation#inheritance)[`super()`](https://shrinerb.com/docs/plugins/validation#inheritance), quedando así:

```ruby
Attacher.validate do
  super()
  validate_max_size 5*1024*1024
end
```

**Sin foto de perfil**

En estos casos queremos poner una imagen por defecto, que indique claramente la ausencia de la foto de perfil. Digamos que tenemos una imagen para este propósito en `/app/assets/images/no-profile-picture.png`. Podemos usar el [plugin ](https://shrinerb.com/docs/plugins/default_url)[`default_url`](https://shrinerb.com/docs/plugins/default_url) para usarla siempre que no haya una imagen de perfil:

```ruby
plugin :default_url

Attacher.default_url do |**options|
  ActionController::Base.helpers.image_url('no-profile-picture.png')
end
```

`ActionController::Base.helpers.image_url` nos ayuda a obtener la url final de uno de los assets del proyecto.

Con esto, todo usuario cuya `profile_picture` sea `nil` retornará la url del asset al hacer `user.profile_picture_url`.

**Distintos tamaños**

Vamos a procesar la imagen para tener dos tamaños aparte del original: `small` y `medium`. Shrine tiene dos opciones para realizar el procesamiento: dinámicamente [cuando se pide](https://shrinerb.com/docs/plugins/derivation_endpoint) la transformación (on-the-fly) o [con anterioridad](https://shrinerb.com/docs/plugins/derivatives), guardando el resultado. Para este ejemplo usaremos la segunda opción.

Como prerequisito necesitamos agregar la gema [image_processing](https://github.com/janko/image_processing). Luego podemos definir las `derivatives` para los tamaños `small` y `medium`:

```ruby
require "image_processing/vips"

...

plugin :derivatives

Attacher.derivatives do |original|
  vips = ImageProcessing::Vips.source(original)

  {
    small:  vips.resize_to_limit!(300, 300),
    medium: vips.resize_to_limit!(500, 500),
  }
end
```

Después en el controlador se debe gatillar la creación de estas derivadas:

```ruby
user = User.new(..., profile_picture: file)

if user.valid?
  user.profile_picture_derivatives! if user.profile_picture_changed?
  user.save
end
```

Y para acceder a la url de una de las derivadas:

```ruby
User.last.profile_picture_url(:small)
```

**Resultado**

Con todo esto, nuestro `Uploader` que hereda de `ImageUploader` quedaría así:

```ruby
require "image_processing/vips"

class ProfilePictureUploader < ImageUploader
  plugin :default_url
  plugin :derivatives

  Attacher.validate do
    super()
    validate_max_size 5*1024*1024
  end

  Attacher.default_url do |**options|
    ActionController::Base.helpers.image_url('no-profile-picture.png')
  end

  Attacher.derivatives do |original|
    vips = ImageProcessing::Vips.source(original)

    {
      small:  vips.resize_to_limit!(300, 300),
      medium: vips.resize_to_limit!(500, 500)
    }
  end
end
```

Y para agregar el attachment al modelo de usuario:

```ruby
include ProfilePictureUploader::Attachment(:profile_picture)
```

### Recursos útiles

* [Documentación oficial](https://shrinerb.com/): muy buena, con guías y secciones explicando los distintos plugins

* [Repo en Github](https://github.com/shrinerb/shrine)

* [Direct S3 Upload](https://github.com/shrinerb/shrine/wiki/Adding-Direct-S3-Uploads) / [Direct App Upload](https://github.com/shrinerb/shrine/wiki/Adding-Direct-App-Uploads): ambos sirven para subir un archivo antes de que se le haga submit a un form. La diferencia radica en que el de S3 lo sube directamente a AWS, mientras que el otro lo sube a un `upload_endpoint` de la aplicación. Para ambientes que no tienen un bucket S3 (como `development`, en que se guardan los archivos en el filesystem) habría que usar Direct App Upload

* [Demo Direct Upload + Vue](https://drive.google.com/file/d/1fwrZ1tLZa_xeSp2j57iKFgjNlgDxXAM9/view?usp=sharing): presentación al equipo de Platanus para introducir Shrine. Se arma un componente Vue para manejar el Direct Upload que puede ser usado dentro de un form de Rails

* [Testing](https://shrinerb.com/docs/testing): en la sección de [Test data](https://shrinerb.com/docs/testing#test-data) dan un ejemplo de un helper que puede ser usado en las factories. En la sección de [Acceptance tests](https://shrinerb.com/docs/testing#acceptance-tests) dan un ejemplo de como agregar un archivo como parámetro en tests de controladores usando `Rack::Test::UploadedFile`

### Recursos útiles para plataneros

* [Implementación Direct Upload](https://github.com/platanus/gret/pull/22): PR implementando Direct Upload para ser usado en ActiveAdmin
