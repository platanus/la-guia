# Utils

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
