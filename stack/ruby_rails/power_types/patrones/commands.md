# Commands

> 🚨 En Platanus dejamos de usar comandos. Ahora estamos usando Active Job como lugar para la lógica de negocio

Los *Comandos* son clases destinadas a realizar operaciones acotadas e
independientes. Se implementan a través de un método `perform` que recibe
argumentos y realiza operaciones con ellos entregando un resultado. También
poseen un generador para construir su estructura,

```bash
$ rails generate command DoSomething foo
```

Esto generará una clase que implementa el método `perform`

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
