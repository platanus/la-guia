# React Navigation

Una de las funcionalidades clave de cualquier aplicación mobile es poder navegar entre distintas pantallas, para esto ocupamos [React Navigation](https://reactnavigation.org/). La navegación es un poco más compleja que en una aplicación web porque las aplicaciones móviles suelen ocupar navegadores nesteados, por ejemplo ocupar un navegador drawer para las secciones principales y dentro de cada una de dichas secciones un navegador de stack. Los navegadores que implementa React Navigation son tres:

* [Stack Navigator](https://reactnavigation.org/docs/hello-react-navigation): Con este navegador cada vez que navegas a una nueva página, esta se agrega al historial como una [lista LIFO](https://www.geeksforgeeks.org/lifo-last-in-first-out-approach-in-programming/). Al ir atrás entonces la página actual sale del historial y se muestra la página desde la que se navegó.

* [Drawer Navigator](https://reactnavigation.org/docs/drawer-based-navigation): Este es el típico navegador de menú de hamburguesa, que está escondido y al mostrarse puedes elegir entre distintas secciones.

* [Tab Navigator](https://reactnavigation.org/docs/tab-based-navigation): Este navegador es el que muestra las secciones abajo y se puede cambiar entre estas.

Existe una cuarta forma útil de cambiar entre pantallas que es mediante el JSX de un componente. Por ejemplo con un operador ternario (en el `return` de un componente):

```plain text
{
  condition ? (
    <Screen1 />
  ) : (
    <Screen2 />
  )
}
```

Esto se ocupa para cambiar de pantallas mediante código, es útil por ejemplo [para cambiar entre pantalla logeada y de login](https://reactnavigation.org/docs/auth-flow)


