# Google Play

Toda la administración de aplicaciónes Android se hace en el portal
de desarrolladores de Google (Play Console).

## Crear una cuenta de desarrollador

Aquí hay un recurso oficial con la información

https://support.google.com/googleplay/android-developer/answer/6112435?hl=es-419

## Como agregar usuarios a la cuenta

Para agregar a un usuario del equipo platanus para poder administrar la cuenta
de google play y publicar aplicaciones, puedes seguir los pasos en el siguiente
recurso oficial:

https://support.google.com/googleplay/android-developer/answer/2528691?es-419

## Ingresar al Developer Console

- Ingresar al [Developer Console](https://play.google.com/apps/publish) de Google Play
- Iniciar sesión con el usuario que tiene permisos para la aplicación en cuestión.
- Seleccionar la applicación de la lista.

## Promover una app desde beta a producción

Como práctica general las aplicaciones seran publicadas por un miembro del equipo
en la seccón beta o alpha. Luego el product-owner debe probar, aprobar y promover
la aplicación a producción.

Para esto, el product-owner debe seguir los siguientes pasos:

- Ingresar a la seccion *Manage Releases*
- Seleccionar el la opcion *Manage production* (o Alpha testing)
- Crear un nuevo release con el botón *Create release*
- En la sección *APKS TO ADD* hacer click en el boton **Add APK from library*
- Seleccionar la versión a publicar.
- En la sección *WHAT'S NEW IN THIS RELEASE?* completar el campo con la información.
- Guardar el release con el botón *Save*
- Revisar el release con el botón *Release*
- Publicar la nueva version con el botón *Start Rollout*

![Google Play](assets/google-play-publish.gif)
