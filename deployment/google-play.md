## Google Play

Como práctica general las aplicaciones seran publicadas por un miembro del equipo en la sección __Beta__ o __Alpha__. Lo ideal es que este proceso este configurado de forma automática, dependiendo del tipo de aplicación. Sin embargo aquí también se explica también la forma manual de subir el `.apk`.

### Subir de forma manual a la play store

- Se debe acceder a la sección de *Manage Releases* en la [Consola de Google Play](https://play.google.com/apps/publish/).
- Ir a la sección *App releases*. Dependiendo del estado de la aplicación:
    - *Pre-registration*
    - *Internal test track*
    - *Closed tracks* → __Alpha__
    - *Open track* → __Beta__
    - *Production track*
- Crear un nuevo release con el botón *Create release*.
- Rellenar la información en cada campo, incluir también el APK. Si no se rellena toda la infromación no se podrá hacer el *Rollout*.
- Guardar el release con el botón *Save*.
- Para preparar el release con el botón *Review*.
- Presionar en *Edit Release*.
- Ir a la sección *Review and roll out release* (Si hay errores aparecerán aquí y tendrás que arreglarlos).
- Presionar *Confirm rollout*

Es importante enviar esta versión al grupo de testers y al Product Owner. Luego el Product Owner debe probar, aprobar y
__promover la aplicación a producción__ (presionando en el botón *Release to Production*). El procedimiento completo para el Product Owner es el siguiente.

### Publicar la aplicación

- Se debe acceder a la sección de *Manage Releases* en la [Consola de Google Play](https://play.google.com/apps/publish/).
- Seleccionar la opcion *Manage production* (o Alpha testing).
- Crear un nuevo release con el botón *Create release*.
- En la sección *APKS TO ADD* hacer click en el boton **Add APK from library*.
- Seleccionar la versión a publicar.
- En la sección *WHAT'S NEW IN THIS RELEASE?* completar el campo con la información.
- Guardar el release con el botón *Save*.
- Revisar el release con el botón *Release*.
- Publicar la nueva version con el botón *Start Rollout*.

![Google Play](assets/google-play-publish.gif)

Links útiles:
- [Upload an app](https://support.google.com/googleplay/android-developer/answer/113469?hl=en)
- [Prepare & roll out releases](https://support.google.com/googleplay/android-developer/answer/7159011)
