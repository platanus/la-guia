# Utils

Las utils son helpers que nos permiten agrupar métodos relacionados. Tienen las siguientes particularidades:

* No tienen lógica del dominio de la aplicación donde se usan.

* Suelen ser clases estáticas.

* Su uso normalmente se repite por toda la aplicación.

## ¿Por qué la usamos?

Usamos utils porque Rails no define un lugar específico para este tipo de helpers. 

En Rails todo lo que no es del dominio de una app se coloca dentro de `/lib`. En platanus reservamos este directorio para toda aquella lógica que sea candidata a convertirse en una gema y no para simples helpers.

## ¿Cómo la usamos?

### Instalación

Vienen con la gema [power-types](https://github.com/platanus/power-types#installation) que normalmente viene instalada en nuestro proyectos generados con [potassium](https://github.com/platanus/potassium).

### Uso básico

Luego de instalada la gema, se corre el generador:

`bin/rails generate util util_name method_1 method_2 method_n`

Por ejemplo si queremos crear una clase util para simplificar el uso de enums podemos hacer:

`bin/rails generate util Enums translate_enum_attr translate_aasm_attr enum_options_for_select`

Esto generará el helper en: `app/utils/enums_utils.rb`

```ruby
class EnumsUtils < PowerTypes::BaseUtil
  def self.translate_enum_attr(class_name, enum, key)
    I18n.t(
      "activerecord.attributes.#{class_name.model_name.i18n_key}.#{enum.to_s.pluralize}.#{key}"
    )
  end

  def self.enum_options_for_select(class_name, enum)
    class_name.send(enum.to_s.pluralize).map do |key, value|
      [translate_enum_attr(class_name, enum, key), value]
    end
  end
end
```

Luego se usaría así:

```ruby
supply_purchase.supply_type = :fertilizer
EnumUtils.translate_enum_attr(Supply, :supply_type, supply_purchase.supply_type) #=> "Fertilizante"

EnumUtils.enum_options_for_select(Supply, :supply_type) #=> [["Fertilizante", :fertilizer], ["Agroquímicos", :agrochemical]]
```

Repasemos las particularidades de las utils contra el ejemplo:

* “No tienen lógica del dominio de la aplicación donde se usan.” → En este caso se cumple ya que `EnumsUtils` podría moverse a otra aplicación y seguiría funcionando. No hay lógica específica del proyecto.

* “Suelen ser clases estáticas.” → Como se puede ver  `translate_enum_attr` y `enum_options_for_select` son métodos de clase. No crea una instancia de `EnumUtils` en ningún momento.

* “Su uso normalmente se repite por toda la aplicación” → Sirve para el modelo `Supply` que usamos en el ejemplo pero también podría servir para cualquier otro modelo de la aplicación.

### Recursos útiles

* [Presentación platanus](https://www.youtube.com/watch?v=vAVq-WxIodI&list=PL4jJY1sbBn7C9aNISuaOwRnmhYKjJYdfA&index=2&t=1944s)

* [Documentación de power types](https://github.com/platanus/power-types#utils)


