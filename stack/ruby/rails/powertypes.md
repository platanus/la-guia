## Power Types

Power Types es una [gema](https://github.com/platanus/power-types) desarrollada por **Platanus** que promueve el uso de estos poderosos patrones: **Services**, **Commands**, **Utils** y **Values**.

Estos se basan en el [SRP (Single Responsability Principe)](https://blog.platan.us/solid-single-responsability), que nos dice que cada clase debe tener **1** sola función.
Por ejemplo, si tenemos un modelo con operaciones complejas como este:

```ruby
class User
  def upgrade_membership
    # ...
  end

  def notify_external_system
    # ...
  end

  def register_payment_card
    # ...
  end
end
```

Deberíamos llevar cada una de sus funciones a Commands o Services independientes:

```ruby
class UpgradeMembership < Command
  # ...
end

class ExternalNotifierService < Service
  # ...
end

class RegisterPaymentCard < Command
  # ...
end
```

Estructurando nuestro código de forma modular y desacoplada tenemos las siguientes ventajas:

* Menos riesgo:  Aislar errores, no pisar variables
* Más claridad, que hace cada clase
* DRYness
* Unit Testing de cada funcionalidad

### Patrones

#### Services - `app/services`

Los *Servicios* son clases Ruby que exponen métodos públicos para llevar a cabo
tareas relacionadas a un contexto en común. Pueden ser generados con la ayuda
del siguiente comando

```
$ rails generate service SomeNameService source
```

Esto generará una clase cuyo nombre termina, por convención en `..Service`

```ruby
class SomeNameService < PowerTypes::Service.new(:source)
  def do_something_now
  end

  def do_something_later
  end
end
```

Luego pueden ser utilizados fácilmente instanciando la clase y llamando a sus métodos

```ruby
service = SomeNameService.new(source: data)
result = service.do_something_now
```

#### Commands - `app/commands`

Los *Comandos* son clases destinadas a realizar operaciones acotadas e
independientes. Se implementan a través de un método `perform` que recibe
argumentos y realiza operaciones con ellos entregando un resultado. También
poseen un generador para construir su estructura,

```
$ rails generate command DoSomething foo
```

Esto generará una clase que implementa el método perform

```ruby
class DoSomething < PowerTypes::Command.new(:foo, :bar)
  def perform(args)
  end
end
```

Luego pueden ser llamados y ejecutados de la siguiente forma,

```ruby
result = DoSomething.for(foo: waffle, bar: pancake)
```

Donde `:foo, :bar` son los argumentos.  Están disponibles en el comando como variables de instancia `@foo, @bar`

#### Utils - `app/utils`

Las utils son módulos Ruby que nos permiten agrupar funciones y procedimientos simples e independientes

```ruby
module MagicTricks
  extend self

  def dissappear(object)
    #blah blah
  end

  def shrink(children)
    #bleh bleeh
  end

  def shuffle(cards)
    #blaah
  end  
end
```

Luego los métodos podrán ser llamados de la siguiente forma

```ruby
MagicTricks.dissapear(rabbit)

# Or

include MagicTricks

dissapear(rabbit)
```

#### Values - `app/values`

Los values corresponden a clases Ruby que pueden ser utilizadas para contener información que no
persiste en la base de datos, y por lo tanto solo existe en memoria.
Entonces si por ejemplo, generamos dinámicamente un reporte, en vez de retornarlo como Hash:

``` ruby
class BuildCleaningReport < PowerTypes::Command.new(:data)
  def perform
    # execute report logic, and finally return:
    {
      date: @date,
      area: cleaned_area,
      duration: cleaning.time,
      effiency: cleaned_area / cleaning.time
    }
  end
end
```

Mejor encapsular el resultado en una clase `Report`:

```ruby
# app/values/report.rb
class Report
  attr_accesor :date, :area, :duration

  def eficciency
    area / duration		
  end
end
```

Estos objetos pueden ser utilizados para mover la información de forma
estructurada dentro de las distintas capas de la aplicación.

### Referencias

Para mayor información sobre esta gema, visita los siguientes vínculos

* [Services, Commands y otros poderosos patrones en Rails](https://blog.platan.us/services-commands-y-otros-poderosos-patrones-en-rails-27c2d3aa7c2e)
* [Anatomy of a Rails Service Object](http://multithreaded.stitchfix.com/blog/2015/06/02/anatomy-of-service-objects-in-rails/)
