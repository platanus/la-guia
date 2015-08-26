Blog - Codigo Banana
====================

Para administrar el blog, se usara el board **platanus** en trello y se debe seguir el flujo de tareas descrito en seccion [trello de platanus](cultura.md#el-trello-de-platanus) de la guia de cultura.

#### Empezar a escribir un borrador

Crea el draft, commitealo en un nuevo branch y haz push del branch.

En la linea de comando puedes hacer

```shell
$ jekyll draft <titulo>
$ git checkout -b <branch-titulo>
$ git commit -a -m "draft(post): <titulo>"
$ git push origin <branch-titulo>
```

> el draft no debe tener la fecha, la fecha se agrega al publicarlo.

Escribelo usando markdown y agrega algunos tags que hagan referencia al contenido. Tambien agregar tu usuario de github como author.

#### Un poco de feedback

Cuando estes listo para recibir feedback, mueve la tarjeta a la columna **Review** y has un pull request a master. Este es el momento para compartir el link al PR para que otros puedan comentar. Compártelo por mail y por slack pare todos sepan.

> Asígnale el PR a alguien para que defina el estado de "listo para publicar" aceptando el PR.

Haz los cambios necesarios en el branch de acuerdo al feedback recibido. Cuando este listo para ser publicado haz `squash` de los commits para que el asignado al PR pueda hacer el merge.

#### Metadata

1. Usar la propiedad `tags` para describir el post (no `categories`)
1. En la propiedad `authors` puedes usar un string o un array. Usa el *Github username* para identificar quien escribió el post.
1. Usa la propiedad `excerpt` *(opcional)* para escribir una frase que describa de que se trata el post. Esto es lo que se muestra en la página principal debajo del titulo del post.

  > El campo `excerpt` es generado automáticamente tomando las primeras lineas del post. Es especialmente util agregarlo manualmente cuando tu post parte con un título o una imagen, porque eso es todo lo que se mostrarará en el index.

Para mas información ve el [Readme](https://github.com/platanus/blog/blob/master/README.MD) del blog
