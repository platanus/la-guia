## RSpec

[RSpec](https://rspec.info/) es una herramienta de testing para probar código en Ruby. Está pensada para hacer Behavior Driven Development, y se usa tanto para tests unitarios como de integración.


### ¿Por qué lo usamos?

RSpec se diferencia de otras herramientas para usar testing porque los tests especifican cómo se supone que la aplicación debería comportarse.

Las especificaciones están escritas en lenguaje simple, por lo que los mensajes de fallos y aceptación son fáciles de entender. Al estar basados en comportamiento, se entiende directamente por qué se está testeando algo, y qué consecuencias tiene que no esté pasando.

Además, los tests son útiles para comprobar que algo nuevo que estemos desarrollando no rompe la funcionalidad anterior. Si intentamos agregar un bug, un test bien hecho puede prevenir que rompamos más cosas que las que estamos arreglando. Por eso, en general los proyectos están configurados también con [CircleCI](https://circleci.com/), que (entre otras cosas) corre los tests cuando hay cambios en algunas ramas, o cuando se hacen Pull Requests.

### ¿Cómo lo usamos?

RSpec viene incluido por defecto en Potassium y los proyectos generados incluyen un archivo de configuración `.rspec`. Además, si un proyecto usa [PowerAPI](./power_api.md), se crean tests automáticamente al correr el generador de PowerAPI.

Para correr todos los tests de un proyecto, se ejecuta `bundle exec rspec`. Si quieres correr los tests de un archivo en específico, puedes darle también la ruta del archivo, por ejemplo: `bundle exec rspec ./spec/integration/api/v1/blogs_spec.rb` corre todos los tests que están en ese archivo.

#### Ejemplo

Los siguientes ejemplos fueron hechos usando Ruby 2.7.1, con RSpec 3.9.0

Si estamos haciendo TDD, lo primero que hacemos es escribir los tests. Vamos a suponer que tenemos una billetera (de BTC, por ejemplo), y queremos partir poniendo y sacando plata. Por ahora no nos interesa saber de dónde viene ni dónde va.

Para empezar, creamos los archivos `lib/wallet.rb` y `spec/wallet_spec.rb`:

En `lib/wallet.rb` va el código Ruby que queremos testear. Por ahora, vamos a implementar la funcionalidad que nos permite agregar plata:

```ruby
class Wallet
  attr_reader :money

  def add_money(amount)
  end
end

```

En el archivo `spec/wallet_spec.rb` va lo que queremos testear:

```ruby
require 'wallet'

RSpec.describe Wallet do
  subject(:wallet) { described_class.new(money) }

  let(:money) { 5 }

  context 'with no initial money' do
    let(:money) { 0 }

    it 'adds money correctly' do
      wallet.add_money 20
      expect(wallet.money).to eq 20
    end
  end
```

Si corremos los tests ahora, van a fallar:

<img src="./assets/rspec-failed-test.png" />

Nuestro método `add_money` no hace nada, así que la billetera siempre queda con su cantidad inicial, 0. Vamos a corregirlo:

```ruby
class Wallet
  attr_reader :money

  def initialize(money)
    @money = money
  end

  def add_money(amount)
    @money += amount
  end
end
```

Después de implementar esto, nuestro test pasa:


<img src="./assets/rspec-successful-test.png" />

No es normal que en nuestras billeteras estemos siempre poniendo plata, así que vamos a testear también que podamos sacar.

Para eso, tomaremos una billetera con plata inicialmente, y vamos a sacarle una parte:

```ruby
RSpec.describe Wallet do
...

  context 'with initial money' do
    let(:money) { 20 }

    it 'substracts money correctly' do
      wallet.remove_money 10
      expect(wallet.money).to eq 10
    end
  end
end
```

Nuevamente, para que nuestro test pase, es necesario que implementemos la funcioonalidad de sacar dinero:

```ruby
class Wallet
...

  def remove_money(amount)
    @money -= amount
  end
end
```

Con esto, nuestros dos test pasan. Nos queda uno solo de los casos básicos. Si tratamos de sacar más plata de la que tenemos, ¿tiene sentido que tengamos plata negativa? Para abordar este caso, vamos a hacer un test para probar que sacar más plata de la que tenemos lanza un error.

```ruby
...
  context 'with no initial money' do
  ...
    it 'fails when trying to remove more money than the wallet has' do
       expect { wallet.remove_money 10 }.to raise_error "Tried to remove more money than what's inside the wallet"
    end
  end
```

Y para hacer que el test pase, modificamos nuestro método `remove_money` para que sea así:

```ruby
def remove_money(amount)
  raise "Tried to remove more money than what's inside the wallet" if amount > @money

  @money -= amount
end
```

Esta vez vamos a correr solamente este test, para ahorrarnos un poquito de tiempo (y aprender a especificar qué test vamos a probar). Para eso, corremos el comando `bundle exec rspec ./spec/wallet_spec.rb:16`, y vemos como nuestro último test pasa:

<img src="./assets/rspec-specific-test.png" />


### Testeo de observers

Cuando corramos tests los observers deben ser apagados. Acá podemos ver una ventaja de desacoplar estas diferentes lógicas, ya que en ambiente de tests no queremos que cada vez que se cree un usuario se envíe un mail por ejemplo.

Además cuando testeamos un observer, la entidad que se está observando no debe ser creada a partir de la factory sino con doubles, los cuales nos van a permitir generar los estados que necesitamos facilmente en la instancia, mientras que al usar la factory probablemente nos tomaría un par de comandos inncesarios. 

A continuación mostaremos ejemplos de como hacerlo, como se forzaría usando la factory y observers encendidos, cuales son sus problemas y como se hace usando doubles con observers apagados.


#### **Observers encendidos y factory**

1. Supongamos que tenemos este modelo, con el atributo `current_stock` y `status`:
   
   ```ruby
    class Product
      include PowerTypes::Observable

      def valid_stock?
        return current_stock === 0
      end
    end
   ```

2. Y que además tenemos un observer que cada vez que se guarda el objeto revisa si el stock está en 0 para mandar un mail al usuario.

  ```ruby
    class ProductObserver 
      after_update: :send_sold_out_mail

      def send_sold_out_mail
        if object.valid_stock?
          ProductMailer.with(user: user).sold_out.deliver_later
        end
      end
    end
  ```

3. También supongamos que la factory de `Product` parte con stock 10 siempre. Si hicieramos el test sin doubles sería de la siguiente manera:
  ```ruby

  describe ProductObserver do
    let(:product) { create(:product) }
    let(:product_mailer) { instance_double('ProductMailer', sold_out: message_delivery) }
    let(:mesage_delivery) do
      instance_double('ActionMailer::MessageDelivery', deliver_later: nil)
    end

    before do 
      product.update!(current_stock: 0)
      allow(ProductMailer).to receive(:with).and_return(product_mailer)
    end

    describe 'after_update: :send_sold_out_mail' do
      context "when product has valid stock" do 
        it 'sends mail to user' do
          expect(product_mailer).to have_received(:sold_out)
          expect(message_delivery).to have_received(:deliver_later)
        end
      end
    end
  end
  ```

Lo que pasa con esta solución es que tenemos que hacer el setup manualmente, imagina lo que se checkea dentro de la función `valid_stock?` son varios atributos además de checkear una función de algún objeto de una relación, por ejemplo:

```ruby
  def valid_stock?
    return current_stock == 0 && status == :in_sale && user.inactive?
  end
```

Acá para tener las condiciones necesarias para que `valid_stock?` retorne `true` tendríamos que crear el producto, cambiarle el stock a 0, el status a `:in_sale` o acceder al usuario y hacer el setup para que `user.inactive?`. En este caso el test quedaría de la siguiente forma y podría complejizarse más el setup, mientras más condiciones se tengan en la función `valid_stock?`.

  ```ruby

  describe ProductObserver do
    let(:product) { create(:product) }
    let(:user) { product.user }
    let(:product_mailer) { instance_double('ProductMailer', sold_out: message_delivery) }
    let(:mesage_delivery) do
      instance_double('ActionMailer::MessageDelivery', deliver_later: nil)
    end

    before do 
      product.update!(current_stock: 0, status: :in_sale)
      user.update!(status: :inactive)
      allow(ProductMailer).to receive(:with).and_return(product_mailer)
    end

    describe 'after_update: :send_sold_out_mail' do
      context "when product has valid stock" do 
        it 'sends mail to user' do
          expect(product_mailer).to have_received(:sold_out)
          expect(message_delivery).to have_received(:deliver_later)
        end
      end
    end
  end
  ```


Es por todo esto que al usar doubles nos ahorramos este trabajo innecesario de setup.

#### **Observers apagados y doubles**

Ahora mostraremos un ejemplo de como hacerlo de la manera correcta con doubles y sin los observers activados:

```ruby

 describe ProductObserver do
    def trigger(type, event, object)
      described_class.trigger(type, event, object)
    end

    describe 'after_update: :send_sold_out_mail' do
      context 'when product has valid stock' do
        let(:product) do
          instance_double(
            "Product",
            valid_stock?: true, # hacemos que la funcón por si sola retorne true
          )
        end
        let(:product_mailer) { instance_double('ProductMailer', sold_out: message_delivery) }
        let(:mesage_delivery) do
          instance_double('ActionMailer::MessageDelivery', deliver_later: nil)
        end

        before do
          allow(ProductMailer).to receive(:with).and_return(product_mailer)
        end

        it 'sends mail to user' do
          trigger(:after, :update, product) # desencadenamos el evento manualmente y no debemos hacer update! del objeto
          expect(product_mailer).to have_received(:sold_out)
          expect(message_delivery).to have_received(:deliver_later)
        end
      end
    end
  end
```

## Testeo Services

El testeo en los services funciona casi como cualquier test, con la única diferencia de que primero se debe instanciar la clase y luego hacer un describe por cada método del  service que se quiere testear. A continuación te mostramos el esqueleto de un ejemplo:

Sigamos con el ejemplo dado en la sección de power api.

```ruby
class BsaleService < PowerTypes::Service.new(:order)
  def create_commercial_invoice
    # code
  end

  def create_credit_note
    # code
  end

  private

  # acá sus métodos privados
end
```

Su respectivo test sería el siguiente:

```ruby
describe BsaleService do 
	let(:order) { create(:order) }

	def service
		describled_class.new(order: order)
	end

	describe "#create_commercial_invoice" do 
		def perform
			service.create_commercial_invoice
		end

		# Cosas que se deben testear del método
	end

	describe "#create_credit_note" do 
		def perform
			service.create_credit_note
		end

		# Cosas que se deben testear del otro método
	end
end
```
### Recursos Útiles

- [RSpec cheat sheet](https://devhints.io/rspec): una lista resumen de cosas útiles que se pueden hacer con RSpec, y de qué manera se pueden testear las condiciones que queremos.
- [Repositorio de rswag](https://github.com/rswag/rswag): es lo que se usa para testear APIs, en el repositorio hay un getting started y ejemplos básicos de tests de APIs.
