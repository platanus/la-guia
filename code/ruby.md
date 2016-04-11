# Ruby

## Version Management

La version actual que usamos se puede ver en [ruby.platan.us/latest](http://ruby.platan.us/latest)

Para administrar las versiones de ruby usamos [rbnev](https://github.com/sstephenson/rbenv)

Los proyectos deben tener la version de ruby definida en el archivo
`.ruby-version` en la raíz del proyecto. Debemos definir versiones
sin incluir el patch. Es decir `2.1` para usar cualquier version `2.1.x`.

Esta version viene definida si usamos
[potassium](http://github.com/platanus/potassium) para inicializar
nuestro proyecto.

También podemos defirla usando

```
rbenv local <alias-version>
```

### Aliases

Para esto debemos tener definidos aliases para las principales versiones:

```shell
$ ls -l /opt/rubies
2.1.6
2.1 -> /opt/rubies/2.1.6 #symlink a la version mayor
2.2.1
2.2.3
2.2 -> /opt/rubies/2.2.3 #symlink a la version mayor
```

{% getPostsByTag 'ruby' %}{% endgetPostsByTag %}
