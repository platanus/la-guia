Manejo de APIs
==============

Para el input y output de las APIs que creamos usamos root objects.

## Qué es un root object

Son wrappers que encapsulan al request que accedemos. Nos permiten extender el request/response que se transmite sin romper funcionalidad existente. Un ejemplo de api rootless es este:

```json
{
  "id": 5,
  "name": "John",
  "last_name": "Doe"
}
```

Con root, esa respuesta viene encapsulada en un objecto `user` que nos da la flexibilidad de agregar otros objectos en la misma respuesta sin romper la compatibilidad con los clientes que consumen la API.

```json
{
  "user": {
    "id": 5,
    "name": "John",
    "last_name": "Doe"
  },
  "meta": {
    "api_version": "2.0",
    "timestamp": "2015-09-25 10:47:29"
  }
}
```

## Rails

La forma de configurarlo es añadiendo la siguiente línea al archivo de configuración `config/application.rb`.


```ruby
ActiveRecord::Base.include_root_in_json = true
```

Por defecto esta configuración ya viene incluída en [Potassium](https://github.com/platanus/potassium).

Para manejar respuestas de una API con root, como la del ejemplo del usuario, hacemos algo así:

```ruby
response = JSON.parse(RestClient.get('https://api.com/users/5')
response["user"]["id"] # 5
response["user"]["name"] # John
response["meta"]["api_version"] # 2.0
```

El código resulta bastante similar al manejo de formularios, que envuelven al recurso en el hash de params: `params[:user]`.

## Restmod

En proceso