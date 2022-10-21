# Expo

Todo el setup requerido se puede encontrar [en la documentación](https://docs.expo.io/get-started/installation/), de todas maneras aquí hay un resumen. Para poder desarrollar con Expo y React Native tendrás que instalar [Expo CLI](https://expo.io/tools#cli) con el comando

```bash
npm install expo-cli --global
```

Además de esto, necesitas un dispositivo en el que correr tu proyecto. Tienes dos opciones:

* Ocupar un dispositivo físico, para lo que tendrás que descargar la aplicación [Expo Client](https://expo.io/tools#client).

* Ocupar un emulador. Si tienes un computador Apple, entonces al instalar Xcode ya instalaste el simulador de iOS. Si tienes otro computador o si quieres probar con un emulador Android en tu Mac, puedes ocupar [Android Studio](https://developer.android.com/studio). Si tienes cualquier problema puedes revisar la instalación detallada de [Apple aquí](https://docs.expo.io/workflow/ios-simulator/) o la de [Android Studio aquí](https://docs.expo.io/workflow/android-studio-emulator/).

Para correr el proyecto, ejecuta el siguiente comando

```bash
expo start
```

Esto levantará un servidor que te permitirá correr tu aplicación mientras desarrollas. Se abrirá una página en tu explorador desde el cual podrás iniciar los emuladores que tengas instalados y mostrará un QR para poder escanearlo si es que quieres ocupar un dispositivo físico. Para iOS debes escanearlo con la cámara y para Android se hace en la misma aplicación Expo Client.

## Crear un proyecto nuevo

Para crear un proyecto, asegurate de tener la última versión del Expo CLI instalada. Luego puedes ocupar el comando

```bash
expo init <project-name>
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
