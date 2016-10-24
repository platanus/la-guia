# Node

## Version Management

La version actual que usamos se puede ver en el [stack node](https://github.com/platanus/our-boxen/blob/HEAD/modules/stacks/manifests/node.pp) de boxen

Para administrar las versiones de node usamos [nodenv](https://github.com/OiNutter/nodenv)

Los proyectos deben tener la version de node definida en el archivo
`.node-version` en la raíz del proyecto. Debemos definir versiones
sin incluir el patch. Es decir `4.1` para usar cualquier version `4.1.x`.

Esta version viene definida si usamos nuestro generador para
[ionic](https://github.com/platanus/generator-platanus-ionic) para
inicializar nuestro proyecto.

También podemos defirla usando

```
nodenv local <alias-version>
```

### Aliases

Para esto debemos tener definidos aliases para las principales versiones:

```shell
$ ls -l /opt/nodes
0.12.37
0.12 -> /opt/nodes/0.12.37 # symlink a la version mayor
4.1.1
4.1 -> /opt/nodes/4.1.1 # symlink a la version mayor
```
