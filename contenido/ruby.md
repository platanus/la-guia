Ruby [:link:](https://github.com/ruby/ruby)
=========

# Estilos

* Evitar comentarios sobre organización (# Validations)
* Evitar `return` explícitos
* Evitar el uso de `;`
* No usar `self` explícitamente excepto en métodos de clase (`def self.method`) y asignación de atributos (`self.attribute =`)
* Preferir `detect` sobre `find`
* Preferir `select` sobre `find_all`
* Preferir `map` sobre `collect`
* Preferir `reduce` sobre `inject`
* Preferir `double` quotes for `strings`
* Preferir `&&` and `||` sobre `and` y `or`
* Preferir `!` sobre `not`.
* Preferir `&:method_name` sobre `{ |item| item.method_name }`
* Usar `_` para argumentos no utilizados
* Usar `%{}` para _strings_ de una línea que necesiten interpolación y comillas dobles. `%{Hola "#{nombre}"}`
* Usar `{...}` para bloques de una linea. Usar `do..end` para bloques de múltiples líneas.
* Usar el sufijo `?` para _predicate methods_
* Usar _CamelCase_ para clases y módulos, _snake_case_ para variables y métodos, _SCREAMING_SNAKE_CASE_ para constantes
* Usar `def self.method`, no `def Class.method`
* Usar `def` con paréntesis cuando hay argumentos
* Usar `each`, no `for`, para iterar
* Usar heredocs para strings multilínea
