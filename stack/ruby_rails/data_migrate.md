# Data Migrate

[Data Migrate](https://github.com/ilyakatz/data-migrate) es una gema que introduce el concepto de migraciones de data, que corren junto a las migraciones normales de rails que modifican el *schema*.

## ¿Por qué la usamos?

El propósito principal de las migraciones normales de Rails es cambiar el *schema* de la aplicación. Sin embargo, a veces es necesario hacer cambios a la data misma, y a veces esos cambios deben estar coordinados con un cambio al *schema*. Se podría manipular la data en una misma migración corriente de Rails, pero `data_migrate` nos permite separar esa responsabilidad y mantenerlo más ordenado.

## ¿Cómo la usamos?

### Instalación

La gema viene instalada si el proyecto se generó usando [Potassium](https://github.com/platanus/potassium). También se incluyen configuración necesaria para que [annotate](https://github.com/ctran/annotate_models) se corra al usar las tasks que nos da la gema. Si por alguna razón el proyecto no tiene la gema, se puede agregar, junto a la configuración mencionada, corriendo `potassium install data_migrate`.

### Generar nueva migración de data

```bash
rails g data_migration backfill_some_column_in_some_model
```

> Warning: Si se crea la migración de datos junto a una de schema hay que asegurarse que no compartan el mismo nombre. Si se llamaran igual, las clases creadas en ambas migraciones también tendrían el mismo nombre y Rails se podría confundir. Ver este issue para más detalles.

### Corriendo las migraciones de schema junto a las de data

> El README de la gema describe todos los comandos que agrega la gema. Como se menciona ahí, también se pueden ver corriendo rake -T data.

Hay que tener en mente que en vez de correr `rake db:migrate`, para correr ambos tipos de migraciones juntas se debe usar `rake db:migrate:with_data`. Este comando también está incluido en el archivo `bin/release` en nuestros proyectos, para que se corra en heroku al hacer deploy.

Usar este comando es importante ya que si se corrieran primero las de *schema* y luego las de *data* podrían haber problemas. Para entender esto consideremos el siguiente ejemplo:

Teníamos un usuario con una columna string `address`. Recientemente se cambió el modelo de datos y `address` pasó a ser su propio modelo `Location` con información adicional. Para esto se realizaron tres migraciones:

1. Una de schema que genera la tabla para el modelo `Location` y agrega la referencia a la tabla de usuarios

1. Una de data que para cada usuario le crea una `Location` y parsea el contenido de la columna `address` al formato del nuevo modelo

1. Una de schema que elimina la columna `address` de los usuarios

Si se corrieran las migraciones por separado usando `rake db:migrate` y luego `rake data:migrate` (orden 1 -> 3 -> 2) la migración de data se caería ya que se eliminó la columna `address` antes. Para esto usamos `rake db:migrate:with_data` que las corre todas en orden de creación.

### Posibles problemas relacionados al deploy

Hay algunos casos en que pueden haber problemas con la aplicación en staging o producción. Uno de estos casos es cuando ocurre un problema y es necesario restaurar un backup y correr las migraciones que se hayan generado entre la fecha del backup y el presente. Esto puede generar problemas con las migraciones de datos si en ellas se accede a cosas que existían a nivel de código cuando se generó el backup pero con la versión actual del código ya no existen. Este no es un problema exclusivo de la gema, siempre que se manipule data en migraciones puede suceder esto.

Para evitar lo anterior hay un par de alternativas:

* Definir un modelo "temporal" en la migración de data, asociado a la tabla que se use, y usar este exclusivamente. Esto nos independiza del modelo real, y nos obliga a usar solo lo que esté efectivamente definido en la tabla al momento de correr la migración de data. Es decir, no se correrán validaciones ni callbacks del modelo original, ni tampoco se podrán usar scopes definidos ahí. Este es el *approach* que mencionan en la [guía de estilo de rails de rubocop](https://github.com/rubocop-hq/rails-style-guide#define-model-class-migrations).

    Como ejemplo, digamos que tenemos un usuario que pasa de tener una columna `boolean` que indica si es gerente o no, a tener un string `role`:

    ```ruby
    class BackfillRoleInUsers < ActiveRecord::Migration[6.0]
      class MigrationUser < ApplicationRecord
        self.table_name = :users
      end
    
      def up
        MigrationUser.all.each do |user|
          user.update!(role: user.is_manager ? 'manager' : 'worker')
        end
      end
    
      def down
        raise ActiveRecord::IrreversibleMigration
      end
    end
    ```

* Usar `ActiveRecord::Base.connection.execute(query)` para correr una consulta SQL directamente, donde `query` es el string con esa query. Esto también nos obligaría a usar solo lo que existe en DB al momento de correr la migración.

### Recursos útiles

* [Repo](https://github.com/ilyakatz/data-migrate)

* [Otro ejemplo de posible problema en deploy](https://medium.com/@jeffcoh23/why-you-should-avoid-activerecord-when-using-ruby-on-rails-data-migrate-gem-2651739395d9): caso detallado en que hay problemas por diferencias entre el código que se usa en la migración de data y el *codebase* del proyecto al momento de correrla en producción. Explica también como reemplazar el código problemático por SQL directo
