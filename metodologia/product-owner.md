## Referencia para el Product Owner

En términos generales, el rol del Producto Owner (PO) es transmitir la visión del producto al equipo y priorizar el desarrollo de acuerdo a esa visión. Esto require tener claro lo que se quiere lograr o el problema que se quiere resolver. Durante el desarrollo, el PO debe conocer a la perfección su producto, tanto en sus funcionalidades como en sus limitaciones.

### tl;dr

#### Qué esperamos del PO

Esperamos que el PO cumpla con un conjunto específico de tareas.

- Que pueda dedicar entre 1/4 y 1/2 de tiempo laboral al proyecto
- Que pueda asistir a las reuniones de sprint
- Que mantenga constantemente priorizadas las funcionalidades y tareas a realizar para que estén alineadas con el negocio
- Que revise periódicamente las tarjetas en la columna "En Revisión". Recomendamos que sea al menos una vez al día.
- Que esté atento al Trello del proyecto para revisar tarjetas y responder preguntas
- Que transmita la visión del producto al equipo de desarrollo

#### Cuándo el PO necesita pedir ayuda para mejorar

Hay varios síntomas que indican que a un PO le está costando cumplir con sus responsabilidades.

- Repetidamente llega al sprint con tarjetas en la columna En Revisión
- Usa la reunión de sprint para revisar las tarjetas
- No responde a las preguntas de Trello en menos de 24 horas hábiles
- No puede juntarse para la reunión de Sprint repetidamente
- No asiste a las reuniones de Sprint, y no avisa que no lo hará
- Cambia de prioridad los objetivos de desarrollo semana a semana

Estos síntomas representan un gran riesgo para el éxito del proyecto. Es muy importante que, de existir alguna de estas situaciones, estas se transparenten con el equipo para tomar las medidas necesarias.

### Cómo trabajamos

#### Equipo típico

El equipo está compuesto, al menos, por los siguientes roles:

- Product Owner
- Scrum Master: se encarga de mantener el proyecto sano. Su principal misión es estar atento y reactivo frente las situaciones que puedan ser riesgosas para el proyecto. Al detectar un riesgo, aplicará las medidas necesarias para reducir la probabilidad de que ocurra, o de reducir su impacto.
- Desarrollador (es): se encargará de desarrollar las funcionalidades del sistema.
- Diseñador@: participará en la creación del diseño del MVP y en todos los ajustes que sean necesarios realizar.

Es importante destacar que estos son roles, y no personas. Aunque no es lo idea, una persona podría actuar en más de un rol.

#### Etapas del desarrollo

###### Kick off

La visión del producto nos hace mirar bien lejos, pero hay que partir por algo más concreto y cercano. En el kick off, el equipo de desarrollo, en conjunto con el PO, participarán en la definición de un MVP (Mínimo Producto Viable). Para lograr definirlo, se necesita tanto la visión de largo plazo (que la aporta el PO), como el contraste con la realidad (que lo dará el equipo de desarrollo).

La idea es lograr definir un MVP que valide las hipótesis más importantes del negocio o producto en el menor tiempo posible. Lo que salga de esa etapa será el foco del desarrollo inicial. Para lograr esto usamos ciertas dinámicas específicas en las que participa todo el equipo.

Producto de esta etapa: definición del MVP, y objetivos que se deben cumplir para lograrlo.

###### Sprints de Diseño

Esta etapa puede ocurrir en paralelo con el inicio del desarrollo, o antes. Por lo general dura 2 - 3 semanas. La idea es obtener un diseño de las pantallas a desarrollar con el respectivo flujo entre ellas.

Esta etapa es sumamente útil e importante, porque nos ayuda a poner en el papel lo que teníamos en la mente. De ahí suelen surgir sorpresas que no teníamos consideradas, y que harán que el equipo se tenga que poner de acuerdo en qué dejar adentro y qué dejar afuera del MVP.

Producto de esta etapa: diseño y flujos del MVP en Invision.

###### Desarrollo del producto

En Platanus intentamos desarrollar de la manera más _lean_ posible. Esto significa que no nos gusta desarrollar cosas que no son necesarias para lograr los objetivos concretos que tenemos definidos.

Por ejemplo: en una reunión sale la idea de que se pueda importar usuarios usando un archivo Excel. Nuestro próximo objetivo es que un usuario pueda responder un formulario. Si bien es cierto que importar a través de Excel es una funcionalidad que puede ser útil en el futuro, no está alineado con nuestro objetivo actual, así que se debe postergar. La idea es ir desarrollando lo justo y necesario.

En general intentamos desarrollar productos que salgan lo antes posible al mercado. Puede que esto implique lanzar una versión muy reducida respecto a la visión del producto a futuro. Incluso puede tener muchas partes manuales aún no automatizadas. Pero consideramos el feedback de usuarios reales demasiado relevante como para postergar la "salida a producción" del MVP.

###### Proceso semanal

Todo el equipo conversa una vez a la semana en un horario fijo. Le llamamanos "Reunión de Sprint". Puede ser presencial o por videollamada. En esta reunión se resolverán dudas sobre las siguientes tarjetas que se deben desarrollar, y el PO transmitirá su visión al resto del equipo sobre el producto y las prioridades.

Para coordinar el desarrollo usamos Trello. Cada funcionalidad a desarrollar será dividida en tarjetas de Trello, que vivirán en alguna columna de acuerdo a su estado. El PO debe estar consciente de la estructura del Trello. Hay dos columnas a las que debe prestar particular atención: "En revisión", y "Backlog".

Supongamos que existe una tarjeta con el título: "El usuario puede loguearse en el sistema". Esta tarjeta partirá en la columna "Backlog", que es donde estarán las siguientes funcionalidades a desarrollar. El desarrollador tomará esta tarjeta y la pasará a la columna "En Progreso". Una vez que haya terminado de desarrollarla, la pasará a "Code review". Esta columna indica que la tarjeta está siendo revisada por un colega para verificar la calidad del código.

La siguiente columna es "En Revisión". __Esta es responsabilidad del PO__. Las tarjetas que estén ahí estarán en un ambiente de pruebas (_staging_). El propósito de éste es probar las nuevas funcionalidades antes de ponerlas a disposición de los usuarios reales. El PO deberá probar la tarjeta en _staging_, y si está bien la debe pasar a "Listo". Si está mal, debe dejar un comentario y devolverla a "Backlog" para que el desarrollador considere los comentarios en el desarrollo.
