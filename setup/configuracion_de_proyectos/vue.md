# Vue

Gracias a Webpacker y [Potassium](https://github.com/platanus/potassium) es muy fácil empezar a usar Vue junto con un proyecto nuevo de Rails.

```plain text
> potassium create PROYECTO_DE_RAILS
[...]
Which front-end framework are you going to use?:
‣ Vue
  Angular
  None
```

En el caso que tengas que agregar Vue a un proyecto Rails 5 que ya fue inicializado, puedes usar webpacker para configurarlo:

```bash
bundle exec rails webpacker:install:vue
```

En Rails 5.2+ hay que configurar el ambiente de desarrollo con lo siguiente:

`config/initializers/content_security_policy.rb`

```plain text
Rails.application.config.content_security_policy do |policy|
  if Rails.env.development?
    policy.connect_src :self, :https, '<http://localhost:3035>', 'ws://localhost:3035'
    policy.script_src :self, :https, :unsafe_eval
  else
    policy.script_src :self, :https
  end
end
```

Una vez instalado, la estructura inicial del proyecto será la siguiente:

```plain text
app/javascript:
  ├── packs:
  │   └── application.js
  └── app.vue
```

```plain text
views/layouts/application.html.erb

<%= javascript_pack_tag 'application' %>
<%= stylesheet_pack_tag 'application' %>
```

Webpacker funciona con *packs*, siendo cada "pack" un bundle de código independiente generado por webpack. En este caso, agregando las tags de arriba la aplicación de Rails va a tener acceso al compilado de todo lo importado en `application.js` y el `scss` generado a partir de los archivos `.vue`.

En un proyecto más avanzado esta sería una estructura ejemplo:

```plain text
app/javascript:
  ├── main
  │   ├── api
  │   │   └── form-api.js
  │   ├── components:
  │   │   ├── sidebar.vue
  │   │   ├── main.vue
  │   │   └── footer.vue
  ├── shop
  │   ├── api
  │   │   └── cart-api.js
  │   ├── components:
  │   │   ├── cart.vue
  │   │   └── cart.spec.js
  │   ├── store:
  │   │   └── index.js
  │   │   └── modules
  │   │       ├── cart.js
  │   │       └── cart.spec.js
  └── packs:
      ├── main.js
      └── shop.js
```

```plain text
views/layouts/application.html.erb

<%= javascript_pack_tag 'main' %>
<%= stylesheet_pack_tag 'main' %>
```

```plain text
views/layouts/shop.html.erb

<%= javascript_pack_tag 'shop' %>
<%= stylesheet_pack_tag 'shop' %>
```
