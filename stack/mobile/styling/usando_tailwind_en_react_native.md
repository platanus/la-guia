# Usando Tailwind en React Native

Para usar tailwind en los proyectos mobile, usamos la librería [tailwind-rn](https://github.com/vadimdemedes/tailwind-rn).

> 💡 Si usas una versión de `tailwind-rn` menor a la 4, hay varias cosas que funcionan muy diferente. La mayoría de lo que aparece en esta página no te será de utilidad. Se recomienda migrar usando [esta guía](https://github.com/vadimdemedes/tailwind-rn/blob/master/migrate.md).



Hay que hacer ciertos pasos previos para poder usar tailwind en los componentes de React Native. Si el proyecto se inició con [cavendish](https://github.com/platanus/cavendish), esto ya se encuentra configurado y puedes saltarte a la sección de uso.

## Setup

Se debe correr el comando `npx setup-tailwind-rn` que instala la mayoría de las dependencias. Luego el mensaje final del script contiene los siguientes pasos a seguir. De igual forma, estos pasos se encuentran descritos en el [README del repositorio de Github de la librería](https://github.com/vadimdemedes/tailwind-rn#install).

## Uso

Como tailwind no está soportado nativamente por React Native, la manera en qué esta librería hace que funcione es creando un archivo  `tailwind.json` en la raíz del proyecto. El archivo va guardando las clases y hace la traducción al sistema de stylesheets de react native.  El archivo puede verse algo como así:

```json
/*
 ...
*/

"items-center": {
		"style": {
			"alignItems": "center"
		}
	},
	"rounded": {
		"style": {
			"borderTopLeftRadius": 4,
			"borderTopRightRadius": 4,
			"borderBottomRightRadius": 4,
			"borderBottomLeftRadius": 4
		}
	},
	"rounded-full": {
		"style": {
			"borderTopLeftRadius": 9999,
			"borderTopRightRadius": 9999,
			"borderBottomRightRadius": 9999,
			"borderBottomLeftRadius": 9999
		}
	},
	"bg-gray-900": {
		"style": {
			"--tw-bg-opacity": 1,
			"backgroundColor": "rgb(17 24 39 / var(--tw-bg-opacity))"
		}
	},
/* 
 ...
*/
```



> 💡 Este archivo no tiene todas las clases existentes de tailwind de una. De hecho al iniciar el proyecto este archivo estará vacío. Si se intenta usar una clase de tailwind que no esté en este archivo,** no va funcionar.**

Cómo se actualiza este archivo? Hay dos formas:

1- Cuando quieras usar una nueva clase de tailwind que no tenías antes, usando el comando 

    ```bash
    yarn build:tailwind
    ```

    se actualizara el archivo `tailwind.json` con todas las clases que se usan en el proyecto.

    Esto se debe hacer cada vez que se quiera agregar una nueva clase y puede ser latero, sobre todo al inicio de un proyecto.  

2- Con el comando 

```bash
yarn dev:tailwind
```

se lanza un servidor que escucha los cambios del proyecto, y cada vez que se agrega una nueva clase de tailwind, actualiza el archivo `tailwind.json`.

Esto es mucho más cómodo que la primera opción y es por eso **la opción que recomendamos usar.**



En los componentes se debe importar el hook `useTailwind` e invocarlo para obtener la variable `tailwind`. Finalmente, en la prop de `style` de los componentes se usa esta variable para poder usar las clases de tailwind en react native!

 

## TLDR

* Inicia el servidor de tailwind con `yarn dev:tailwind` 

* Importa el hook `useTailwind` de la librería `tailwind-rn`

* `const tailwind = useTailwind()` dentro del componente que estás escribiendo

* En la prop `style` de los componentes, escribir `tailwind('clases de tailwind aquí')`








