Rails [:link:](https://github.com/rails/rails)
=========

* Inicializamos nuestros proyectos con [potassium](https://github.com/platanus/potassium)
* El deploy se hace con [negroku](https://github.com/platanus/negroku)
* Cuando nuestros proyectos incluyen una API Rails, utilizamos la gema [simple_token_authentication](https://github.com/gonzalo-bulnes/simple_token_authentication) para manejar la autenticación. Más información en este [blog post](http://cb.platan.us/rails/authentication/restmod/angular/2015/03/13/usando-angular-auth-lib-con-simple-token-authentication-gem.html)
* Las interfaces de administración las contruimos usando [Active Admin](/contenido/activeadmin.md)
* Para resolver el problema de autenticar usuarios que no pueden registrarse libremente en una aplicación utilizamos [devise_invitable](http://cb.platan.us/rails/active%20admin/devise/2015/03/18/invitar-usuarios-con-devise.html)

## Estilos

#### Formato

* Nombrar columnas de tipo `date` con sufijos `_on`
* Nombrar columnas de tipo `datetime` con sufijos `_at`
* Nombrar columnas de tipo `time` con sufijo `_time`
* Ordenar las asociaciones de `ActiveRecord` por el tipo de asociación
* Ordenar las asociaciones de `ActiveRecord` sobre las validaciones de `ActiveRecord`
* Ordenar los contenidos del controlador de la siguiente forma: filtros, métodos públicos y métodos privados
* Ordenar los contenidos del modelo de la siguiente forma: constantes, macros, métodos públicos y privados
* Organizar los _partials_ de la aplicación en el directorio `app/views/application`
* Preferir `def self.method` sobre `scope :method`
* Usar `render 'partial'` sobre `render partial: 'partial'`
* Usar `link_to` para peticiones _GET_, y `button_to` para otros verbos _HTTP_

#### Routes

* Evitar el uso de la opción `:except` en las _routes_
* Usar la opción `:only` para exponer rutas explícitamente
