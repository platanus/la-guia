Power Types
===

Powert Types es una [gema](https://github.com/platanus/power-types) desarrollada por 
*Platan.us* que permite estructurar el código en base a 4 prácticas (o patrones) 
conocidos como **Services**, **Commands**, **Utils** y **Values**.

El principal beneficio de trabajar con Power-Types es que nuestro código quedará 
ordenado y no será una sorpresa a la hora de ser mantenido por otros desarrolladores.


## Services - `app/services`

Los *Servicios* son clases Ruby que expone métodos públicos para llevar a cabo 
tareas relacionadas a un contexto en común. Pueden ser generados con la ayuda 
del siguiente comando

```
$ rails generate service SomeNameService source
```

Esto generará una clase cuyo nombre termina, por convención en `..Service`

```Ruby
class SomeNameService < PowerTypes::Service.new(:source)
	def do_something
	end
end
```

Luego pueden ser utilizados fácilmente instanciando la clase y llamando a sus métodos

```Ruby
ham = Mono.new
result = ham.do_something
```

## Commands - `app/commands`

Los *Comandos* son clases destinadas a realizar operaciones acotadas e 
independientes. Se implementan a través de un método `perform` que recibe 
argumentos y realiza operaciones con ellos entregando un resultado. Tamibén 
poseen un generador para construir su estructura,

```
$ rails generate command DoSomething foo 
```

Esto generará una clase que implementa el método perform

```Ruby 
class DoSomething < PowerTypes::Command.new(:foo)
	def perform(args)
	end
end
```

Luego pueden ser llamados y ejecutados de la siguiente forma,

```Ruby
result = DoSomething.for(foo: bar)
```

Donde `foo: bar` es simplemente el argumento `args` que será entregado al método `perform`.

## Utils - `app/utils`

Las utils son módulos Ruby que nos permiten agrupar funciones y procedimientos. 

```Ruby
module SomeModule
	extend self

	def do_something
	end

	def do_other
	end
end
```

Luego los métodos podrán ser llamados de la siguiente forma

```Ruby
SomeModule.do_something()
# Or
include SomeModule
do_something()
```

## Values - `app/values`

Los values corresponden a clases Ruby que pueden ser utilizadas para contener 
información que no persiste en la base de datos, y por lo tanto solo existe 
en memoria.

```Ruby
class SomeClass
	attr_accessor :name, :email

	def initialize(name, email)
		@name = name
		@email = email
	end

	def do_something
	end

	private

	def some_private_method
	end
end
```

Estos objetos pueden ser utilizados para mover la información de forma 
estructurada dentro de las distintas capas de la aplicación.

### Referencias

Para mayor información sobre esta gema, visita los siguientes vínculos

* [Services, Commands y otros poderosos patrones en Rails](https://blog.platan.us/services-commands-y-otros-poderosos-patrones-en-rails-27c2d3aa7c2e)
* [Anatomy of a Rails Service Object](http://multithreaded.stitchfix.com/blog/2015/06/02/anatomy-of-service-objects-in-rails/)