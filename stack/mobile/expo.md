# Expo

[Expo](https://expo.io/) es un ecosistema de herramientas que facilitan el uso de React Native. Apoyan el desarrollo, build e incluso publicación a las stores. El proceso de build se refiere a generar la aplicación nativa en iOS (`.ipa`) o Android (`.apk` o `.aab`).

Las herramientas que ofrecen son:

* [Expo Client](https://expo.io/tools#client): aplicación para iOS y Android en la que podrás correr tu proyecto mientras desarrollas. Esto no requiere hacer un build. También permite compartir proyectos entre miembros del equipo antes de subirlos al store.

* [Expo CLI](https://expo.io/tools#cli): command line interface que permite montar un servidor para correr el proyecto en Expo Client localmente, hacer build y publicar los proyectos

* [Expo Snack](https://expo.io/tools#snack): herramienta online para correr Expo en el browser, permite probar y compartir pequeñas aplicaciones o ejemplos

* [Expo SDK](https://expo.io/tools#sdk): SDK que provee acceso a las APIS nativas como la cámara, ubicación, acelerómetro, notificaciones, y muchos más.

Actualmente los SDKs tienen [actualización trimestral](https://dev.to/expo/expo-sdk-37-is-now-available-69g#time-based-releases). Es importante mantenerse al día y [actualizar el proyecto](https://docs.expo.io/workflow/upgrading-expo-sdk-walkthrough/) con cada nuevo SDK. Primero porque estas actualizaciones suelen traer muchas novedades y arreglo de errores. Segundo porque la actualización paulatina es mucho más fácil que actualizar varias versiones al mismo tiempo y permite manejar los breaking changes de cada uno sin que se acumulen. Tercero porque con cada release se depreca el SDK más antiguo. Si tu proyecto ocupa un SDK deprecado las aplicaciones publicadas seguirán funcionando, pero no podrás seguir ocupando Expo Client o volver a hacer build.


