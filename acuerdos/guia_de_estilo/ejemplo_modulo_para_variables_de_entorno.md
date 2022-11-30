# Ejemplo: Módulo para variables de entorno



```ruby
# lib/my_module.rb
module MyModule
  MY_NUMBER = ENV.fetch("MY_NUMBER", 10)
  MY_BOOLEAN = ENV["MY_BOOLEAN"]

  def self.my_number
    MY_NUMBER.to_i
  end

  def self.my_boolean?
    MY_BOOLEAN == "true"
  end
end

# se acceden así: 
MyModule.my_number
	MyModule.my_boolean?
```

Definimos un módulo en donde los fetch de las variables de entorno están fuera de los métodos (de esta manera si una variable de entorno no está definida fallará el build). Dentro de los métodos sólo se encuentra el formateo de las variables

Para que el módulo esté disponible en la aplicación, se debe agregar el require en el archivo `application.rb`

```ruby
# application.rb
config.before_configuration do
   require Rails.root.join("lib/my_module.rb")
end
```


