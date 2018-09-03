Ionic
=====

(Aplicaciones móviles)

## Stack

* Usamos [Angular 5+](https://angular.io) para construir nuestras aplicaciones usando tecnología web.
* Junto con [Ionic 3](https://ionicframework.com/) para correrlas en dispositivos móviles.
* Y [NgRx](https://github.com/ngrx/platform) para el manejo de estado, _side-effects_ y _routing_ cuando corresponde.
* Estamos explorando [NgXs](https://ngxs.gitbook.io/ngxs) para el manejo de estado de una forma menos verbosa.



## Arquitectura de nuestras aplicaciones móviles

Si bien usar `Angular` + `Ionic` + `NgRx` resulta en una estructura de aplicaciones bastante bien definida, en la experiencia hemos descubierto que aún existe espacio donde uno puede ponerse demasiado creativo y terminar con una aplicación muy compleja.

~Nuestros porrazos~ Nuestra experiencia nos ha llevado al siguiente manifiesto de arquitectura a la hora de desarrollar aplicaciones usando este framework.

## Manifiesto aplicaciones Angular/Ionic

### Clasificación de componentes

Los componentes de las aplicaciones se clasifican en dos tipos:

 - **Componentes inteligentes**: Son aquellos a los que les interesa saber el estado de la aplicación y por lo tanto se conectan con el `Store`.
 - **Componentes "tontos"**: Son componentes que viven dentro de componentes "inteligentes" y toda la información que necesitan se las entrega su contenedor. Por lo tanto, nunca deberían informar acciones ni recibir cambios de estado directamente del `Store`

### Estructura y jerarquia de componentes

 - Todos los elementos gráficos de la aplicación deben estar en una carpeta independiente y funcionar como componentes.
 - Las vistas móviles o páginas (`Pages`) son componentes inteligentes que se encargan de la lógica en general y son los responsables de pasar la información necesaria a los componentes que son "dummy" ya que no manejan ningún tipo de estado.

### Interacción entre vistas, el Store, Providers y Components

 - Las vistas (`Pages`) son las encargadas de despachar `Actions` al `Store`
 - La `Actions` podrían gatillar un `Effect` que invoca a un `Provider`.
 - Son los `Provider` los encargados de rescatar o enviar información al mundo exterior.
 - Al completarse la ejecución de un `Provider` se gatilla una nueva `Action` informando del éxito o fracaso de la operación.
 - **Las vistas NO invocan servicios directamente**.

#### Ejemplo del funcionamiento de la interacción entre una vista, el `Store` y otros componentes es:

 - Una `Page` gatilla una `Action`, p.ej: `UpdateCalendarEventsAction`
 - El `Store` se encarga de gatillar el `Effect` correspondiente `LoadCalendarEventsEffect`
 - El `Effect` invoca al `Provider` `CalendarEventsProvider` para recuperar la información que requiera. Si la recupera correctamente gatilla la accion `UpdateCalendarEventsSuccess` o gatilla `UpdateCalendarEventsFailure` en caso de error.
 - El `Store` se encarga de refrescar el estado de la aplicación al recibir la acción `UpdateCalendarEventsSuccess`.
 - Los cambios se propagan desde el `Store` hacia los componentes a través de las suscripciones que tengan los componentes con el estado de la App.

### Navegación de la app

Usar Ionic o enlazarlo con el store como por ejemplo se muestra [aqui](https://gist.github.com/llekn/e93dccb2b3dba1744949497a1b054798)

### Preguntas varias

- **¿Cómo manejamos la navegación?**
  + Usamos las facilidades de Ionic o enlazarlo con el `Store` como por ejemplo se muestra [aqui](https://gist.github.com/llekn/e93dccb2b3dba1744949497a1b054798)
- **¿Los errores de ejecución en servicios (p.ej llamada HTTP) se manejan en los mismos servicios  o en las acciones que los invocan?**
  + Los `Effects` deben ser los encargados de manejar los errores (tienen el `catchError` al invocar el `Provider`). Luego de esto el `Effect` invoca una `Action` indicando el error.
- **¿Cómo se loguean los errores en requests HTTP?**
  + Si se requiere algo especial, usar un [HTTPInterceptor](https://gist.github.com/llekn/895436cbfae7486cfd93c92f62c5e50e).
- **¿Declarar las `Actions` como clases o sólo como strings?**
  + Las `Actions` como clases mientras evaluamos `NgXs`
- **¿Cuánta información incluir en el `payload` de la acción?**
  + Enviar la mínima información relacionada con la `Action`.
  + El `Effect` o `Action` son los responsables de recuperar toda la información adicional desde el `Store` para actualizar el estado de la app o invocar la siguiente `Action` o `Effect`.
- **¿Cómo nombramos a los `Providers` y qué funciones cumplen?**
  + Seguimos la convención `Ionic` (El archivo se llama `eventos.ts` dentro de la carpeta `providers` y la clase se llamaría `EventProvider`. Mas en detalle:
    - Si el `Provider` se usa en el `Store`, entonces debe estar dentro de la carpeta del modulo del `Store` correspondiente.
    - Si no (o son usados por más de un módulo del store), debe estar dentro de la carpeta `app/providers`
- **¿Cómo accedemos al estado de la app?**
  + Hacemos que los distintos módulos del `Store` exporten selectores que se importan en el índex del store y a su vez este exporta los selectores globales.
    - Después cada componente lo importa desde el índex del `Store`.
    - Nunca usar algo que no sea un selector.
- **¿Cómo nos suscribimos a eventos globales?**
  + Suscribirnos en el `app.component` pero teniendo cuidado con que no crezca mucho
