ActiveAdmin
===========

* Para integrar Devise Invitable usamos [esta gema](https://github.com/platanus/admin-invitable). Más información en este [blog post](https://blog.platan.us/invitar-administradores-con-admin-invitable-gem)
* Para mejorar la interacción en ActiveAdmin le agregamos [ActiveAdminAddons](https://github.com/platanus/activeadmin_addons)

#### Casos Típicos

##### Manejo de `has_and_belongs_to_many` con `select2`

Supongamos que tenemos los siguientes modelos:

*my_app/app/models/doctor.rb*

```ruby
class Doctor < ActiveRecord::Base
  has_and_belongs_to_many :specialities
end
```
*my_app/app/models/specialty.rb*

```ruby
class Specialty < ActiveRecord::Base
  has_and_belongs_to_many :doctors
end
```

Si queremos agregar especialidades a un doctor en `ActiveAdmin` usando `select2` debemos:

1. Instalar la gema [ActiveAdminAddons](https://github.com/platanus/activeadmin_addons)
1. Tener en `my_app/admin/doctor.rb` el siguiente código:

```ruby
ActiveAdmin.register Doctor do
  permit_params :speciality_ids => []

  form do |f|
    f.inputs "Información" do
      # another attributes...
      f.input :specialities
    end
    f.actions
  end
end
```

y eso es todo! el atributo `:specialties` será reconocido como una relación `has_and_belongs_to_many` y la gema hará el resto.

##### Filtrar por múltiples valores usando select2

Se debe agregar el atributo `multiple: true` al filtro, de esta manera:

```ruby
filter :specialities, multiple: true
```
