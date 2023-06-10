# Crear y conectar una slice en Redux

##  Qué es una slice?

Una slice contiene una porción de data del estado de la aplicación, y métodos para procesar, cambiar y acceder a esa data. 

Las slices de la app van en la carpeta `src/store/slices`  con el nombre del modelo de datos que quieren representar, en camelCase. Por ejemplo, la slice que maneja los datos del usuario debería llamarse `user.ts` (o `currentUser.ts`) y su path de importación debería ser `src/store/slices/user` (o `@/store/slices/user` usando el alias `@`).



Hay 3 cosas importantes que proporciona una slice: 

* El estado inicial de la data de ese modelo

* Cómo debería modificarse el estado de la data (`actions` y `reducers`)

* Shortcuts customizables para acceder a la data (`selectors`)

## Crear una slice

  Importamos la función `createSlice` de la librería `redux-toolkit`, que simplifica muchísimo todo el proceso de definir la data y escribir los reducers. Primero definimos un estado inicial de la slice, y le damos un nombre. Vamos a crear una slice para un contador, que va a partir con cero como valor inicial:

```typescript
import { createSlice } from ''@reduxjs/toolkit';

const initialState = {
	value: 0,
};

const counterSlice = createSlice({
  name: 'counter',
  initialState,
	// otras cosas...
});
```

  Ahora debemos especificar en la slice cómo va a cambiar el estado de la data (en este caso, del valor de contador). La función `createSlice` viene con un campo `reducers` en donde podemos especificar justamente eso.

  En teoría, para cambiar la data necesitamos definir `actions` y `reducers`, pero la función `createSlice` nos permite preocuparnos solamente de escribir las funciones reducers y a partir de eso genera las actions correspondientes. Por ejemplo, si queremos tener un reducer que incremente el valor del contador en uno, debemos definirla de la siguiente manera:



```typescript
//...

const counterSlice = createSlice({
  name: 'counter',
  initialState,
	reducers: {
    increment: (state) => {
      state.value += 1;
		},
  }
});
```



  Además, estas funciones pueden recibir información adicional de lo que deben actualizar a través del campo `payload` de una `action`. Por ejemplo, imaginemos que ahora queremos crear un reducer `incrementBy`, que le suma al contador un número variable:

```typescript
import { createSlice, type PayloadAction } from ''@reduxjs/toolkit';
//...
const counterSlice = createSlice({
  name: 'counter',
  initialState,
	reducers: {
    // ...
    incrementBy: (state, action: PayloadAction<number>) => {
			state.value += action.payload;
		},
  }
});
```



 

Agregando  un reducer para disminuir el valor del contador por un número arbitrario, y también otro reducer para devolver a cero el contador, la versión final del archivo `src/store/slices/counter.ts` queda así: 

```typescript
import { createSlice, type PayloadAction } from '@reduxjs/toolkit';

const initialState = {
  value: 0,
};

export const counterSlice = createSlice({
  name: 'counter',
  initialState,
  reducers: {
    reset(state) {
      state.value = 0;
    },
    incrementBy(state, action: PayloadAction<number>) {
      state.value += action.payload;
    },
    decrementBy(state, action: PayloadAction<number>) {
      state.value -= action.payload;
    },
  },
});
```



 Ya tenemos una slice que tiene data inicial y provee formas de manipularla a través de los reducers que definimos, pero todavía no está conectada a la store de la app. Para eso debemos  agregar los reducers que definimos al archivo `src/store/reducers.ts` en donde se encuentra el reducer de toda la aplicación (el `appReducer`).



El archivo `src/store/reducers.ts` se debería ver parecido a algo como esto:



```typescript
import { combineReducers } from '@reduxjs/toolkit';

import { userSlice } from '@/store/slices/user';
import { postsSlice } from '@/store/slices/posts';

const appReducer = combineReducers({
  user: userSlice.reducer,
  posts: postsSlice.reducer,
});

export default appReducer;
```

 Aquí se encuentra el `appReducer`, que cómo bien dice su nombre, es el reducer que maneja los cambios de estado de toda nuestra aplicación. Este reducer se obtiene usando la función `combineReducers` de `redux-toolkit`.



Para agregar el slice que creamos, tenemos que dentro del objeto que se le pasa a `combineReducers` agregar el campo `counter`y que su valor sea el reducer de nuestra slice.

> ⚠️ OJO! el campo que se agrega a `combineReducers` tiene que coincidir con el nombre que se le dio a la slice dentro de `createSlice`



Finalmente el archivo con el `appReducer` queda así:

```typescript
import { combineReducers } from '@reduxjs/toolkit';

import { counterSlice } from '@/store/slices/counter';
import { userSlice } from '@/store/slices/user';
import { postsSlice } from '@/store/slices/posts';

const appReducer = combineReducers({
  counter: counterSlice.reducer,
  user: userSlice.reducer,
  posts: postsSlice.reducer,
});

export default appReducer;
```

Listo 🥳! Ahora tenemos acceso a la data, las acciones y los reducers que definimos en nuestro archivo original, y ya podemos llamarlos dentro de nuestros componentes de React 💯


