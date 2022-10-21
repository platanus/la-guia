# Services

Los servicios son objetos de Ruby destinados a separar la lógica de negocios del resto de la aplicación. Éste permite agrupar muchos métodos que pertenezcan a la misma lógica de negocios.

A menudo pueden ser confundidos con `Clients`, ya que su estructura es bastante similar: una clase que agrupa métodos de una misma lógica. La diferencia entre estos radica en su función más general, como explicamos antes los servicios agrupan lógica de negocios (de la aplicación), mientras que los clientes son hechos para comunicarnos con con servicios externos, como por ejemplo consumir una API. Este podría ser removido de la aplicación actual, ser utilizado en otra y seguir funcionando. Un servicio puede consumir un cliente.

Acá puedes ver un servicio de `Bsale` (sistema de ventas) que agrupa 2 métodos que toman una orden (modelo de la aplicación), crean un factura o una nota en `Bsale` (a través de un `Client`) y guardan el documento generado en la base de datos del proyecto.

```bash
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

### **Servicios vs Jobs**

Los jobs tienen una función similar a la de los servicios, separar la lógica de negocios del resto de la aplicación, sin embargo, estos separan una función en específico, mientras que el servicio puede separar un grupo de funciones que apuntan a la misma lógica.

Aún así, podemos replicar el funcionamiento de un servicio con varios jobs bajo un mismo namespace, donde cada uno tiene una función específica correspondiente a uno de los métodos del service.

```plain text
- Jobs
  - Bsale
    - base_job.rb
    - create_commercial_invoice_job.rb
    - create_credit_note_job.rb
```

```ruby
class Bsale::CreateCreditNoteJob < ApplicationJob
  def perform(resource)
    # code
  end
end
```

Entonces, ¿Cuándo debemos usar cada una de estas opciones? La verdad es que no hay ninguna regla que diga cuándo usar alguno de estos, por lo que queda a gusto del consumidor, pero podemos nombrarte los pro y contras de cada opción.

Al usar servicios es fácil compartir lógica en todos sus métodos mediante algún método privado, es más cómodo encontrar los métodos que encapsulan toda esa lógica de negocios en un solo archivo, sin embargo, es fácil que este empiece a crecer con lógica que realmente no pertenece a ese espacio, quizá lógica necesaria para llevarlo a cabo, pero que no debería estar ahí.

Al usar jobs el compartir lógica entre todos se debe realizar mediante un `base_job`  y todos los demás jobs deben heredar de este, los archivos están separados por cada función, lo cual lo hace un poco más verboso, sin embargo, al tener en cada job una función específica, no se presenta el problema de hacer crecer estos archivos con lógica que no pertenece a ellos, o es más fácil de detectar.
