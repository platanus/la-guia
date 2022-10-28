# Circle CI

Ocupamos [CircleCI](https://circleci.com/) para el linting y testing de nuestros PRs. Cuando creamos un nuevo proyecto con Potassium la configuración de circleci ya está en `.circleci/config.yml` pero tenemos que decirle a CircleCI que tenemos un nuevo proyecto que integrar. Para esto debemos entrar al [CircleCI de Platanus](https://circleci.com/add-projects/gh/platanus): Una vez logueado, buscar repositorio y presionar `Follow Project`.

Por defecto Circle CI intenta adivinar qué es necesario para correr los tests pero hay excepciones para las cuales es necesario agregar un archivo de configuración `circle.yml`:

### Variables de Entorno

Para realizar deploy en ciertos ambientes hay que agregar información a las variables de entorno, como nombres de usuario o API keys.

1. Buscar ⚙️ en el proyecto seleccionado.

1. Seleccionar `Environment Variables`

1. Seleccionar `Add Variable`
