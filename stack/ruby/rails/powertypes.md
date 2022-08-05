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


#### Observers - `app/observers`

Un observer es una clase que, como dice su nombre se encarga de observar al modelo que lleva su nombre y tienen una labor muy parecida o igual a los [callbacks](https://guides.rubyonrails.org/active_record_callbacks.html) de modelos.

Por ejemplo si queremos llamar una función cada vez que se crea una instancia de un modelo lo podemos hacer de la siguiente manera:

```code 
$ rails generate observer MyModel
```

Esto generará un observer de MyModel 

```ruby
  class MyModelObserver < PowerTypes::Observer
    after_create: :puts_hello

    def puts_hello
      puts 'hello'
    end
  end
```

Luego en MyModel debemos añadir:
```ruby 
  class MyModel < ActiveRecord::Base
    include PowerTypes::Observable
  end
```

Ahora cada vez que se ejecute 

```code 
  MyModel.create()
```

se ejecurará puts_hello.


Esto también se puede hacer con callbacks de la siguiente manera:

```ruby 
  class MyModel < ActiveRecord::Base
    after_create: :puts_hello

    def puts_hello
      puts 'hello'
    end
  end
```

Como pueden ver las dos formas son equivalentes. Entonces ¿Por qué usar observers?
#### **¿Por qué ocupar observers?**

Porque nos permite desacoplar lógicas de los modelos que no están directamente relacionadas con ellos.

#### **¿Cuál es el criterio para poner algo en un callback o en el observer?**


Por un lado en los callbacks va todo lo necesario para mantener la integridad del objeto, por ejemplo, el formateo de un rut. Si guardamos un objeto sin formato de rut y todos los demás están formateados, entonces ese modelo en sí estará "corrupto".

Por otro lado, en observers debería ir toda la lógica que está relacionada con el modelo pero que no es necesaria para mantener la integridad de este, por ejemplo, el envío de un mail.

Entonces la regla general sería algo como: ¿Mi objeto puede vivir sin esto?, si la respuesta es sí, entonces va en un observer, si la respuesta es no, va en un callback.


Por último la lógica no debe estar literalmente dentro del observer, lo mejor es que en el observer se llame algún job, comando, value, etc y que estos manejen la lógica. Por ejemplo:

```ruby
  class SalesObserver
    after_update :add_to_sales_report

    def generate_report
      AddToSalesReportJob.perform_later(object) # En este job va toda la lógica
    end
  end
```

#### Services - `app/services`

Los servicios son objetos de ruby destinados a separar la lógica de negocios del resto de la aplicación. Éste permite agrupar muchos métodos que pertenezcan a la misma lógica de negocios.

A menudo pueden ser confundidos con `Clients`, ya que su estructura es bastante similar: una clase que agrupa métodos de una misma lógica. La diferencia entre estos radica en su función más general, como explicamos antes los servicios agrupan lógica de negocios (de la aplicación), mientras que los clientes son hechos para comunicarnos con con servicios externos, como por ejemplo consumir una api. Este podría ser removido de la aplicación actual, ser utilizado en otra y seguir funcionando. Un servicio puede consumir un cliente.

Acá puedes ver un servicio de `Bsale` (sistema de ventas) que agrupa 2 métodos que toman una orden (modelo de la aplicación), crean un factura o una nota en `Bsale` (a través de un `Client`) y guardan el documento generado en la base de datos del proyecto.

```
$ rails generate service BsaleService order
```

Esto generará una clase cuyo nombre termina, por convención en `..Service`

```ruby
class BsaleService < PowerTypes::Service.new(:order)
  def create_commercial_invoice
    return unless @order.invoiceable?

    document = client.post_commercial_invoice(@order)
    return unless document.success?

    save_document(document, 'commercial_invoice')
    @order.generate_invoice!
  end

  def create_credit_note
    return if invoice.nil?

    invoice_details = client.get_commercial_invoice_details(invoice)
    return unless invoice_details.success?

    credit_note = client.post_credit_note(@order, invoice, invoice_details)
    return unless credit_note.success?

    document = client.get_document(credit_note)
    return unless document.success?

    save_document(document, 'credit_note')
    @order.cancel_invoice!
  end

  private

  def client
    @client ||= BsaleClient.new
  end

  def invoice
    @invoice ||= @order.last_invoice
  end

  def save_document(document, document_type)
    @order.last_payment.documents.create!(
      document_type: document_type,
      document_url: document.url,
      document_identifier: document.id
    )
  end
end
```

Luego pueden ser utilizados fácilmente instanciando la clase y llamando a sus métodos.

```ruby
service = BsaleService.new(order: order)
result = service.create_credit_note
```

**Servicios vs Jobs**

Los jobs tienen una función similar a la de los servicios, separar la lógica de negocios del resto de la aplicación, sin embargo, estos separan una función en específico, mientras que el servicio puede separar un grupo de funciones que apuntan a la misma lógica. 

Aún así, podemos replicar el funcionamiento de un servicio con varios jobs bajo un mismo namespace, donde cada uno tiene una función específica correspondiente a uno de los métodos del service.

```
- Jobs
  - Bsale
    - base_job.rb
    - create_commercial_invoice_job.rb
    - create_credit_note_job.rb
```

``` ruby
class Bsale::CreateCreditNoteJob < ApplicationJob
  def perform(resource)
    # code
  end
end
```

Entonces, ¿Cuándo debemos usar cada una de estas opciones? La verdad es que no hay ninguna regla que diga cuando usar alguno de estos, por lo que queda a gusto del consumidor, pero podemos nombrarte los pro y contras de cada opción.

Al usar servicios es fácil compartir lógica en todos sus métodos mediante algún método privado, es más cómodo encontrar los métodos que encapsulan toda esa lógica de negocios en un solo archivo, sin embargo, es fácil que este empiece a crecer con lógica que realmente no pertenece a ese espacio, quizá lógica necesaria para llevarlo a cabo, pero que no debería estar ahí.

Al usar jobs el compartir lógica entre todos se debe realizar mediante un `base_job`  y todos los demás jobs deben heredar de este, los archivos están separados por cada función, lo cual lo hace un poco más verboso, sin embargo, al tener en cada job una función específica, no se presenta el problema de hacer crecer estos archivos con lógica que no pertenece a ellos, o es más fácil de detectar.

### Referencias

Para mayor información sobre esta gema, visita los siguientes vínculos

* [Services, Commands y otros poderosos patrones en Rails](https://blog.platan.us/services-commands-y-otros-poderosos-patrones-en-rails-27c2d3aa7c2e)
* [Anatomy of a Rails Service Object](http://multithreaded.stitchfix.com/blog/2015/06/02/anatomy-of-service-objects-in-rails/)
