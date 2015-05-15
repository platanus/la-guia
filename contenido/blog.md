Blog - Codigo Banana
====================

Para administrar el blog, se usara el board **platanus** en trello y se debe seguir el flujo de tareas descrito en seccion [trello de platanus](cultura.md#el-trello-de-platanus) de la guia de cultura.

#### Empezar a escribir un borrador

Crea el draft, commitealo en un nuevo branch y has push al branch.

En la linea de comando puedes hacer

```shell
$ jekyll draft <titulo>
$ git checkout -b <branch-titulo>
$ git commit -a -m "adds the <titulo> draft"
$ git push origin <branch-titulo>
```

> el draft no debe tener la fecha, la fecha se agrega al publicarlo.

Escribelo usando markdown y agrega algunos tags que hagan referencia al contenido. Tambien agregar tu usuario de github como author.

#### Un poco de feedback

Cuando estes listo para recibir feedback, mueve la tarjeta a la columna **Code Review** y has un pull request a master. Este es el momento para compartir el link al PR para que otros puedan comentar. Puedes compartirlo por mail y por chat si quieres.

#### Últimos detalles

Haz los cambios necesario en el branch de acuerdo al feedback recibido. Cuando este listo para ser publicado haz `squash` de los commits y asignale el PR a alguien para que sepa que esta listo para ser publicado.

Para mas informacion ve el [Readme](https://github.com/platanus/blog/blob/master/README.MD) del blog
