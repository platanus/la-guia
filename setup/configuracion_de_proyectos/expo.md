# Expo

Todo el setup requerido se puede encontrar [en la documentación](https://docs.expo.io/get-started/installation/), de todas maneras aquí hay un resumen. Para poder desarrollar con Expo y React Native debes tener Node instalado. Se recomienda  usar una versión LTS de Node (las versiones con números pares). Se recomieda usar [Nodenv](https://la-guia.platan.us/setup/configuracion_de_tu_entorno_local/tecnologias/node).

Además de esto, necesitas un dispositivo en el que correr tu proyecto. Tienes dos opciones:

* Ocupar un dispositivo físico, para lo que tendrás que descargar la aplicación [Expo Go](https://expo.dev/client).

* Ocupar un emulador. Si tienes un computador Apple, entonces al instalar Xcode ya instalaste el simulador de iOS. Si tienes otro computador o si quieres probar con un emulador Android en tu Mac, puedes ocupar [Android Studio](https://developer.android.com/studio). Si tienes cualquier problema puedes revisar la instalación detallada de [Apple aquí](https://docs.expo.io/workflow/ios-simulator/) o la de [Android Studio aquí](https://docs.expo.io/workflow/android-studio-emulator/).

Para correr el proyecto, ejecuta el comando

```bash
yarn start
```

o el comando 

```bash
npx expo start
```

Cualquiera de los comando anteriores levantará un servidor que te permitirá correr tu aplicación mientras desarrollas. Se abrirá una página en tu explorador desde el cual podrás iniciar los emuladores que tengas instalados y mostrará un QR para poder escanearlo si es que quieres ocupar un dispositivo físico. Para iOS debes escanearlo con la cámara y para Android se hace en la misma aplicación Expo Go.

## Crear un proyecto nuevo

Para crear un proyecto nuevo con la plantilla inicial de Typescript  usa el comando

```bash
npx create-expo-app <project-name> --template expo-template-blank-typescript
```

## Proyecto existente

Si vas a empezar a trabajar en un proyecto que alguien ya creó.

```bash
git clone platanus/<project-name>
cd <project-name>
yarn install
```

### Conectar la aplicación con el servidor local

Es muy probable que al desarrollar necesites conectar la aplicación con el servidor de rails que estás corriendo en tu computador. Para poder hacer esto, tienes que correr el servidor con el siguiente comando

```bash
bundle exec rails s -b 0.0.0.0
```

Luego, puedes acceder al servidor desde la aplicación ocupando [la dirección IP interna de tu computador](https://lifehacker.com/how-to-find-your-local-and-external-ip-address-5833108). El servidor debería encontrarse en `http://<ip-interna>:3000`.
