# AlpineJS

## ActiveAdmin + AlpineJS

AlpineJS es una excelente alternativa para agregar algo de inteligencia a nuestras vistas de ActiveAdmin. En la mayoría de los casos podemos definir todo lo que usamos directamente en nuestro recurso de ActiveAdmin, sin necesidad de archivos JS externos.

## Instalando AlpineJS

```bash
# Si estamos usando Shakapacker, Webpack 5+ u otro bundler moderno
yarn add alpinejs

# Si estamos usando Webpacker
yarn add alpinejs@2
```

Después tenemos que agregar lo siguiente al archivo donde ActiveAdmin se inicializa, normalmente el que tiene la linea `import '@activeadmin/activeadmin';`

```javascript
import '@activeadmin/activeadmin';

import Alpine from 'alpinejs';

window.Alpine = Alpine;
Alpine.start();
```

Si estás usando Alpine 2 (por Webpacker), tienes que usar lo siguiente:

```javascript
import '@activeadmin/activeadmin';

import 'alpinejs'
```

> 💡 Todos los ejemplos en esta guía y el repositorio asociado usan AlpineJS 3 pero deberían funcionar en AlpineJS 2.

## Intro

Un componente de AlpineJS es un elemento HTML con el atributo (también llamado directiva) `x-data` con todas las variables que vamos a usar dentro de un objeto de javascript.

```html
<div x-data="{open: false}"></div>
```

Para que esto funcione en ActiveAdmin, tenemos que agregar el atributo `x-data` a `inputs`, `input` o cualquier otro elemento "wrapper"

```ruby
f.inputs 'x-data':  CGI.escapeHTML("{...#{f.resource.attributes.to_json}}") do
  f.input :name
end
```

> 💡 Tenemos que usar `CGI.escapeHTML` para evitar que el objeto producido por Rails no escape del atributo `x-data`, lo que normalmente pasa por una comilla doble.

> 💡 `f.resource.attributes` nos da acceso a todos los atributos del modelo. En vez de usarlo, también puedes declarar los valores a mano, siempre recordando que el resultado final debe ser un objeto válido de javascript.

> 💡 Si un ejemplo en la guía no incluye el atributo `x-data`, de todas maneras se asume que fue declarado

Una vez que hemos inicializado el componente con `x-data` podemos empezar a usar las otras directivas de AlpineJS.

```ruby
f.inputs 'x-data':  CGI.escapeHTML("{...#{f.resource.attributes.to_json}}") do
  f.input :name, input_html: { 'x-model': 'name' }
end
```

## Formatear el valor de un campo al escribir

Casos de uso: Números de teléfono, valores en formato de moneda local, números de cédula de identidad, etc.

Para empezar tenemos que agregar la función para formatear en el mismo archivo donde inicializamos Alpine y exponer la función a la página en ActiveAdmin.

```javascript
window.Alpine = Alpine;
Alpine.start();

window.formatters = {
  // Formats a number to currency
  currency: new Intl.NumberFormat('es-CL', { style: 'currency', currency: 'CLP' }),
  // Removes everything that's not a number from a string
  numberCleaner(value) {
    return value.replaceAll(/\\D/g, '');
  },
};
```

Después, dentro de nuestro recurso en ActiveAdmin, podemos usar `x-on-input` (o `@input`) para formatear el valor cada vez que escribimos un valor en el campo de texto.

> 💡 Para este ejemplo en especifico, tenemos que limpiar el número (para obtener `1000` en vez de `1.000`), asi que ejecutamos `numberCleaner` antes de `format.

```ruby
f.input :amount, input_html: {
  'x-model': 'amount',
  'x-on:input': 'amount = formatters.currency.format(formatters.numberCleaner($event.target.value));'
}
```

Si actualizamos la página el monto no estará formateado porque el formateador solo se ejecuta con el evento `input`. Para arreglar esto tenemos que editar el valor iniciar en `x-data`.

```ruby

f.inputs 'x-data': CGI.escapeHTML("{ amount: formatters.currency.format('#{f.resource.attributes['amount']}')}") do

  f.input :amount, input_html:
    'x-model': 'amount',
    'x-on:input': 'amount = formatters.currency.format(formatters.numberCleaner($event.target.value));
    '
```

En el caso que el atributo del modelo sea un `integer` en la base de datos, no podremos guardar el valor formateado. Para lograr guardar el valor actualizado pero mantener el formato al editarlo tenemos que agregar un par de cosas para tener dos campos: el campo formateador y el campo "real" que se guarda en la base de datos.

En el modelo agregamos:

```ruby
class FormatFieldExample < ApplicationRecord
  attr_accessor :active_admin_amount
end
```

> 💡 `attr_accessor` es necesario ya que ActiveAdmin no permite mostrar valores que no existen como campos en su formulario.

Después, en el archivo de ActiveAdmin, agregamos `amount` directamente y además agregamos el campo `active_admin_amount` formateado a `x-data`.

```ruby
f.inputs 'x-data': CGI.escapeHTML("{
    amount: #{f.resource.attributes['amount']},
    active_admin_amount: formatters.currency.format('#{f.resource.attributes['amount']}')},
  }") do
```

Reemplazamos el campo `amount` por `active_admin_amount` y en el evento `x-on:input` agregamos que también se actualice `amount`.

```ruby
  f.input :active_admin_amount, input_html:
    'x-model': 'active_admin_amount',
    'x-on:input': '
      active_admin_amount = formatters.currency.format(formatters.numberCleaner($event.target.value));
      amount = formatters.numberCleaner(active_admin_amount);
    '
```

Finalmente agregamos un campo oculto con el `amount` real para que se guarde en la base de datos como número.

```ruby
f.input :amount, as: :hidden, input_html: {
  'x-bind:value': 'amount'
}
```

[ejemplo](https://github.com/platanus/activeadmin-alpinejs-examples/blob/main/app/admin/format_field_examples.rb)

## Validar un campo

Casos de uso: Prevenir que un formulario se pueda guardar si un valor no es válido, mostrar cuando un campo es obligatorio o tiene un valor inválido.

Al igual que en el ejemplo anterior, tenemos que agregar la función de validación a la variable `window` para que esté disponible en la página de ActiveAdmin.

```ruby
import { rutValidate } from 'rut-helpers';

window.validators = {
  // Formats a value to the standard RUT format.
  rut: rutValidate
};
```

En este ejemplo queremos cambiar la clase CSS del campo cuando el valor no es válido. Para esto necesitamos usar `x-bind:class`(o `:class`) para que la clase `error` sea agregada dinámicamente cuando `validators.rut(rut)` sea `false`:

```ruby
f.input :rut, input_html: {
  'x-model': 'rut',
  'x-bind:class': '{error: !validators.rut(rut)}'
}
```

Si también queremos desactivar el botón para guardar, podemos editar la acción `submit` para agregar el atributo `disabled`.
`x-bind:disabled` (o `:disabled`) automáticamente agregan el atributo cuando la validación falla.

```ruby
f.actions do
  f.action :submit, button_html: { 'x-bind:disabled': "!validators.rut(rut)" }
end
```

[ejemplo](https://github.com/platanus/activeadmin-alpinejs-examples/blob/main/app/admin/validate_field_examples.rb)

## Esconder y mostrar un campo

Para poder mostrar y esconder un campo podemos usar la directiva `x-show`.

Primero necesitamos un campo con `x-model` para tener acceso a su valor.

```ruby
f.input :has_description, input_html: {
  'x-model': 'has_description'
}
```

Después agregamos la directiva `x-show` al campo que queremos mostrar o esconder dependiendo del valor que tenga el campo `has_description`.

```ruby
f.input :description, wrapper_html: {
  'x-show': 'has_description'
}
```

> 💡 Tenemos que usar `wrapper_html` en vez de `input_html` para esconder toda la fila, tanto el `label` como el `input`.

[ejemplo](https://github.com/platanus/activeadmin-alpinejs-examples/blob/main/app/admin/toggle_field_examples.rb)

## Campos Select2

[ActiveAdmin Addons](https://github.com/platanus/activeadmin_addons) transforma todos los `select` para que usen Select2, para facilitar el uso de colección grandes o tags. Sin embargo, AlpineJS no tiene idea qué hacer con los elementos de Select2 y viceversa.

Para que funcionen los elementos `select` con atributos `x-model` tenemos que instalar [active-admin-alpine-fixes](https://www.npmjs.com/package/active-admin-alpinejs-fixes).

```bash
yarn add active-admin-alpine-fixes
```

Después tenemos que agregar el fix a la variable `window` para que esté disponible en la página de ActiveAdmin

```bash
import { select2 } from 'active-admin-alpine-fixes';

window.alpineFixes = { select2 };
```

Finalmente agregamos el fix a nuestro component agregando la directiva `x-init` para que ejecute el fix apenas el componente sea evaluado por el navegador.

```ruby
f.inputs 'x-init': 'alpineFixes.select2.init', 'x-data':  CGI.escapeHTML("{...#{f.resource.attributes.to_json}}") do
  f.input :choices, input_html: { 'x-model': 'choices' }
end
```

[ejemplo](https://github.com/platanus/activeadmin-alpinejs-examples/blob/main/app/admin/select2_examples.rb)

## Has Many

ActiveAdmin nos permite tener formularios anidados cuando un recurso tiene un `has_many`. Pero cuando hacemos click en el botón para crear un recurso nuevo en este formulario anidado ActiveAdmin usa jQuery para crear los campos nuevos y AlpineJS se confunde.

Para que funcionen tenemos que instalar [active-admin-alpine-fixes](https://www.npmjs.com/package/active-admin-alpinejs-fixes).

```bash
yarn add active-admin-alpine-fixes
```

Después tenemos que agregar el fix a la variable `window` para que esté disponible en la página de ActiveAdmin

```java
import { hasMany } from 'active-admin-alpine-fixes';

window.alpineFixes = { hasMany };
```

En nuestro componente tenemos que agregar el fix a la directiva `x-init` y en nuestro `x-data` tenemos que agregar explícitamente el recurso anidado. Dentro del `has_many` tenemos que usar `x-model` con el índice que nos da para que AlpineJS sepa a qué campo corresponde qué elemento en el arreglo.

```ruby
f.inputs 'x-init': 'alpineFixes.hasMany.init',
          'x-data': CGI.escapeHTML("{
            ...#{f.resource.attributes.to_json},
            children: #{f.resource.children.to_json}
          }") do
  f.has_many :children, allow_destroy: true do |co, i|
    # has_many index starts with 1 while javascript's starts with 0 so we subtract one
    co.input :name, input_html: {
      'x-model': "children[#{i - 1}].name"
    }
  end
end
```

[ejemplo](https://github.com/platanus/activeadmin-alpinejs-examples/blob/main/app/admin/has_many_examples.rb)

## Formularios Complejos (Solo AlpineJS 3 - Alpine.data)

Si nuestro formulario es muy complejo *o* tiene funcionalidad que puede ser fácilmente re-usada, podemos usar `Alpine.data` en nuestro javascript para declarar un objeto que puede ser usado en nuestro formulario sin tener que usar `window`. En otras palabras, podemos tener un archivo JS separado con todo lo que necesitamos.

```javascript
// activeadmin/complex_example.js

export default (attributes = {}) => {
  function init() {
    // We need to pass the Alpine context (this) so it can find the element
    select2.init.bind(this)();
  }

  const currencyFormat = new Intl.NumberFormat('es-CL', { style: 'currency', currency: 'CLP' });

  function numberCleaner(value) {
    return value.replaceAll(/\\D/g, '');
  }


  // We return an object that will be available inside our component
  return { ...attributes, init, currencyFormat, numberCleaner };
};
```

```java
import complexExample from './activeadmin/complex_example';

Alpine.data('complexExample', complexExample);
Alpine.start();
```

Una vez hecho lo anterior, podemos usar `complexExample` en nuestro `x-data`, el que recibe los atributos que necesitamos para inicializar el objeto que usa Alpine.

> 💡 Como complexExample todavía no se ejecuta, currencyFormat todavía no está disponible para ser usado en x-data. Puedes agregar la función a la variable window, procesar los atributos dentro de complexExample o, como en este caso, usar Ruby para lograr el mismo resultado.

```ruby
form do |f|
  f.inputs 'x-data': "complexExample(#{CGI.escapeHTML("{
      ...#{f.resource.attributes.to_json},
      active_admin_amount: '#{number_to_currency(f.resource.attributes['amount'])}'
    }")})" do
    f.input :name

    f.input :active_admin_amount, input_html: {
      'x-model': 'active_admin_amount',
      # We can use currencyFormat and numberCleaner directly since the are available inside
      # the data object returned by the complexExample function.
      'x-on:input': '
        active_admin_amount = currencyFormat.format(numberCleaner($event.target.value));
        amount = numberCleaner(active_admin_amount);
      '
    }

    f.input :choices, input_html: { 'x-model': 'choices' }

    f.input :amount, as: :hidden, input_html: {
      'x-bind:value': 'amount'
    }

    f.actions do
      f.action :submit
    end
  end
end
```

[ejemplo](https://github.com/platanus/activeadmin-alpinejs-examples/blob/main/app/admin/complex_examples.rb)
