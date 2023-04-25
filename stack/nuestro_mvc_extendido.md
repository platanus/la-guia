# Nuestro MVC extendido

Rails es la base de nuestro stack, y como quizás ya sabes tiene un modelo MVC:

* `Models` que definen la estructura de la data y se comunican con la base de datos

* `Views` que definen la UI - lo que ve el usuario

* `Controllers` que sacan data de los modelos y ponen a disposición de la vista correspondiente la información que necesiten

En Platanus hemos adaptado un poco este modelo MVC, agregando distintas capas y herramientas que nos ayudan a tener responsabilidades más separadas, cosa de mantener nuestros modelos y controllers lo más *skinny *posible.

Puedes encontrar explicaciones más detalladas de estas tecnologías en Stack , la idea de esta sección es explicar un poco como interactúan estas distintas partes:

* Power API: herramienta para generar **controladores de API**. Estos, a diferencia de los controladores de app normales, no tienen una vista `.html.erb` asociada, si no que retornan un json. El front (o una app mobile por ejemplo) puede hacer request a estos endpoints de API y obtener este json sin necesidad de recargar la página.

* Active Admin: herramienta que nos da una manera fácil y rápida de definir interfaces de administración. Básicamente con una sintáxis simple crea controllers y vistas que le permiten a un admin hacer el CRUD tradicional sobre un modelo en particular.

* Pundit: introduce el concepto de **Policy, **clases que funcionan como una capa de **autorización **en los controllers y en Active Admin. Chequean si el usuario actual tiene permitido acceder a una acción en particular.

* https://github.com/heartcombo/devise: funciona como una capa de **autenticación**, osea permite iniciar sesión y requerir que esta esté activa en ciertas acciones. Se usa en controllers y Active Admin

* https://github.com/rails-api/active_model_serializers: introducen el concepto de **Serializers**, clases que toman una instancia de un modelo y define qué atributos/métodos de éste se incluyen en un json.

    Se usan implícitamente en los controlers de API al llamar a `respond_with`. También los usamos explícitamente en vistas `.html.erb` cuando se necesita pasar un objeto como prop a un componente Vue, con el helper `serialize_resource` que nos facilita Power API.

* https://github.com/drapergem/draper: introduce el concepto de **Decorators**, clases donde va la lógica de presentación asociada a un modelo, que puede ser usada en varias vistas. En otras palabras, métodos que podrían ser de instancia de un modelo en particular, pero que serían únicamente para ser usados en vistas, entonces lo separamos de la lógica propiamente tal en el modelo.

* Active Job  y Services: los jobs son el principal lugar donde definimos la **lógica de negocios** (además de tareas que se deban encolar o requieran recurrencia), dado que el MVC tradicional no tiene un lugar obvio para ésta. Es común verlos en controllers con un `perform_now` encapsulando lógica que va más allá del CRUD simple y el manejo de la response/request que hace el controller. También es común verlo en Observers con un `perform_later`, gatillando algún side-effect debido al update o create de un record.

    Los Services, aunque menos comunes, también se usan para lógica de negocio. La diferencia es que exponen varios métodos, y la idea es que estos estén fuertemente relacionados y hagan uso de los mismos parámetros.

* Observers: clases asociadas a un modelo que gatilla “algo” frente a cambios en un record. Tiene los mismos [callbacks que un modelo](https://guides.rubyonrails.org/active_record_callbacks.html), pero la idea es que en el observer vaya todo cambio que **sea un side-effect externo a la instancia.** En otras palabras, todo cambio que deba ir en un callback que toque al objeto mismo debe ir en el modelo, y todo efecto externo (mandar una notificación a slack por ejemplo), en el observer.

* [DRAFT] Clients: clases dónde se definen las interacciones con alguna **página web externa**, ya sea llamadas a una **API o scrapping**. Es común verlo en Job que haga algo con la respuesta, o directo en un controller u observer.

* Data Migrate: gema que separa las migraciones de datos de las de schema. Rails tiene el concepto de migraciones, pero estas definen la manera de cambiar la estructura de la base de datos. Hay veces que no solo se quiere cambiar la estructura (columnas y tablas), sino que también la data misma (las filas). Para eso, usamos migraciones de data por separado.

* Vue: framework de JS que usamos para armar la UI. Usamos `.html.erb` todavía para algunas cosas, pero la mayoría de nuestro front lo estamos construyendo en Vue. La interacción más común que tenemos es que **Rails maneja el ruteo**, pero las vistas se construyen con Vue. Esto quiere decir que tenemos controladores no-API con sus acciones, y esas acciones tienen un `.html.erb` asociado, y dentro de esa vista es común que solo haya un componente Vue que recibe props desde Rails.

* [Tailwind](https://tailwindcss.com/): framework CSS que usamos para estilizar nuestras vistas. Prácticamente no usamos CSS puro o SCSS, en casi todos los casos usamos Tailwind. Se usa en vistas `.html.erb` o en componentes Vue

En este diagrama se muestran más o menos las interacciones descritas:

<img src='assets/nuestro-mvc-extendido-1.svgz'/>

https://app.diagrams.net/#G16C9xcj5y6dPcNtThtR819z8DFbTTasOe


