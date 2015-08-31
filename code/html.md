HTML
====

##Guía de estilo

### `<!DOCTYPE html>`

### Body
* Usamos UTF-8 (`<meta charset="UTF-8">`) en nuestras aplicaciones
* `<meta http-equiv="X-UA-Compatible" content="IE=Edge">` para evitar que IE decida usar la "[vista de compatibilidad](http://stackoverflow.com/questions/6771258/whats-the-difference-if-meta-http-equiv-x-ua-compatible-content-ie-edge-e)"

### Formato General
* Elementos anidados deberían estar en su propia linea e indentados a menos que sean "inline".
* Las etiquetas que se cierran, se **cierran** (`<p></p>`). En las que se cierran solas se omite el `/` final (`<img>`,`<input>`,etc).

````html
<div>
  <p>Nombre: <strong>QBasic on Rails</strong></p>
  <p><img src="http://i.imgur.com/I3A5YSE.gif"></p>
  <ul>
    <li><span>5</span> estrellas</li>
  </ul>
</div>
````

### Formularios
* Cada `input`, `select` o `textarea` dentro de un formulario debería estar acompañado de un `label` con su respectivo atributo `for`.
* Las propiedades _booleanas_ se deberían usar por si solas. `<input type="checkbox" checked>` en vez de `<input type="checkbox" checked="checked">`

Basado en [codeguide.co](http://codeguide.co) y [Primer Guidelines](http://primercss.io/guidelines).
