# Configuración Proyectos

Todo proyecto Platanus debería contar dos características: Hound y CI (Travis o Circle CI), siendo una tercera semi-opcional (dependiendo del proyecto) el proceso de deploy automático desde CI.

## Hound
Hound es el linteador interno que usamos en Platanus, cariñosamente llamado "el mono", que se encarga de revisar cada uno de los PR que pasan por nuestros repositorios para ver si cumplen con nuestras guías de estilo de Ruby, JS, TypeScript y CSS.

### Cómo activarlo:
1. Login en [MonkeyCI] (dar permisos correspondientes)
2. Buscar repositorio (hacer click en "Include private repos" si es necesario)
3. Hacer click en `Activate`

Con eso ya cada PR estará pasando por las manos del mono.

### Detalle en proyectos Open Source:
En proyectos Open Source lo ideal es incluir las reglas de los linters directamente en el repositorio, para facilitar a otros desarrolladores el seguir nuestras guías de estilo. Esto se puede lograr fácilmente haciendo uso de [hound-cli]:

```
hound rules update --local
```

## Continuous Integration
Con CI (ya sea Circle CI o Travis) podemos revisar que los tests de cada proyecto pasen, recibiendo feedback en nuestros commits y PRs antes de mergear. Además, en varios proyectos, con CI hacemos deploy automáticamente tras mergear a master.

### Circle CI (Repositorios privados)
[https://circleci.com/add-projects/gh/platanus]: Una vez logueado, buscar repositorio y presionar `Follow Project`.

Por defecto Circle CI intenta adivinar qué es necesario para correr los tests pero hay excepciones para las cuales es necesario agregar un archivo de configuración `circle.yml`:

#### Variables de Entorno
Para realizar deploy en ciertos ambientes hay que agregar información a las variables de entorno, como nombres de usuario o API keys.

1. Buscar <img src="http://i.imgur.com/xSSLO2x.png" width="32"> en el proyecto seleccionado
2. Seleccionar `Environment Variables`
3. Seleccionar `Add Variable`

#### Potassium
Con Potassium cualquier proyecto Rails que use la receta `heroku` va a traer el archivo que necesita CircleCI para hacer su trabajo.
El deploy se configura desde [Heroku](../deployment/rails)

#### Ionic
Cualquier proyecto generado con `platanus/generator-pl-ionic` va a incluir el script necesario para que CircleCI pueda hacer deploy a Ionic Apps. Es necesario agregar las variables ambientes `IONIC_EMAIL` y `IONIC_PASSWORD`.

### Travis (Repositorios Públicos - Open Source)
[https://travis-ci.org/profile/platanus]: Una vez logueado togglear el estado del repositorio. Al igual que Circle CI, Travis por defecto intenta adivinar qué es necesario para correr los tests de un proyecto pero para agregar deploy hay que agregar un archivo de configuración `.travis.yml`:



