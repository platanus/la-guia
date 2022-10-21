# Strong Migrations

[Strong Migrations](https://github.com/ankane/strong_migrations) es una gema para evitar acciones inseguras a la hora de aplicar migraciones de schema en las bases de datos.

## ¿Por qué la usamos?

Muchas veces al crear migraciones en Active Record, se pueden generar errores inesperados o incluso *downtime* de las aplicaciones dado que pueden generar locks en tablas enteras. Strong migrations ayuda a detectar ese tipo de potenciales errores de manera temprana y ofrece alternativas para realizar los mismos cambios de manera segura.

## ¿Cómo la usamos?

La gema strong migrations viene incluida por defecto en los proyectos generados con Potassium que cuenten con base de datos. En proyectos que no lo tengan, basta con agregar la gema al `Gemfile` y correr el generador `rails generate strong_migrations:install`, como se señala [aquí](https://github.com/ankane/strong_migrations#installation). Alternativamente se puede usar potassium para agregar una base de datos al proyecto con `potassium install db [--force]`, que a su vez incluirá la gema.

Al correr cualquier migración, strong migrations revisará si la migración es segura de acuerdo a los lineamientos propuestos en la gema. En caso de no serlo arroja una excepción, de modo que las migraciones inseguras no se puedan ejecutar correctamente. Dicho error además incluye las instrucciones para realizar la migración de la manera recomendada y segura.

## Casos típicos

Existen una serie de migraciones que son potencialmente peligrosas detalladas en la documentación de la gema, junto con sugerencias de mejores prácticas y verificaciones particulares para el caso de postgres.

Algunas operaciones peligrosas que es muy común realizar de manera insegura son las siguientes:

* [Eliminar una columna](https://github.com/ankane/strong_migrations#removing-a-column)

* [Agregar una columna con un valor por defecto](https://github.com/ankane/strong_migrations#adding-a-column-with-a-default-value)

* [Modificar una columna para que sea NOT NULL](https://github.com/ankane/strong_migrations#setting-not-null-on-an-existing-column)

## Proyectos chicos

En algunos casos las verificaciones que realiza esta gema pueden ser innecesarias si el proyecto está en una etapa temprana o es muy acotado, debido a que ciertos problemas solo surgen cuando el tráfico de las tablas es grande y el tamaño también. Lo anterior también aplica para proyectos más avanzados pero que deseen modificar tablas de la base de datos que no cuenten con información, o que presentan un uso muy limitado.

Si efectivamente es el caso y se cree que los riesgos de correr dichas migraciones son bajos o nulos, la gema ofrece la opción de realizar la migración de todas maneras. Por ejemplo si se desea renombrar una columna, strong migrations alerta sobre los riesgos, pero se puede marcar la operación como segura de la siguiente forma:

```ruby
class RenameSomeColumn < ActiveRecord::Migration[6.0]
  def change
    safety_assured { rename_column :users, :some_column, :new_name }
  end
end
```

Sin embargo, es bueno tener estas prácticas incorporadas en los equipos de desarrollo, para cuando la aplicación efectivamente se enfrente a escalas donde sea importante seguir las recomendaciones.

### Recursos útiles

* [Repositorio](https://github.com/ankane/strong_migrations): tiene la documentación y las migraciones que detecta la gema. Para cada operación se especifica la forma insegura de realizarla, junto con la razón y la forma recomendada de hacerla. También se detallan sugerencias y configuraciones propias de la gema.
