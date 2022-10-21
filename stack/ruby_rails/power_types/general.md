# General

Power Types es una [gema](https://github.com/platanus/power-types) desarrollada por **Platanus** que promueve el uso de estos poderosos patrones: **Services**, **Commands**, **Utils** y **Values**.

Estos se basan en el [SRP (Single Responsability Principe)](https://blog.platan.us/solid-single-responsability), que nos dice que cada clase debe tener **1** sola función. Por ejemplo, si tenemos un modelo con operaciones complejas como este:

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

* Menos riesgo: Aislar errores, no pisar variables

* Más claridad, que hace cada clase

* DRYness

* Unit Testing de cada funcionalidad

## Referencias

Para mayor información sobre esta gema, visita los siguientes vínculos:

* [Services, Commands y otros poderosos patrones en Rails](https://blog.platan.us/services-commands-y-otros-poderosos-patrones-en-rails-27c2d3aa7c2e)

* [Anatomy of a Rails Service Object](http://multithreaded.stitchfix.com/blog/2015/06/02/anatomy-of-service-objects-in-rails/)
