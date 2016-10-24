Angular Unit Testing
=====================

Usamos [jasmine 2.3](http://jasmine.github.io/2.3/introduction.html) como framework de testing.

```javascript
// fields-srv_spec.js
describe('Fields Service', function(){
  ...
});
```

### Los módulos

Si estamos testeando un servicio, queremos aislarlo lo más posible (ej: no queremos que el router de la aplicación nos afecte), por lo tanto es necesario incluir solamente los módulos que sean necesarios. Probablemente cargar la app completa no sea buena idea.

```javascript
//app.js
angular.module("app", ["app.providers", "app.services"]);
angular.module("app.providers", []);
angular.module("app.factories", []);
angular.module("app.services", ["app.providers", "app.factories"]);
```

Si vamos a testear un servicio, carguemos sólo el módulo de servicios

```javascript
beforeEach(module('app.services'));
```

### Inyección de componentes

Usamos esta sintaxis para inyectar servicios y factories

```javascript
var FieldsSrv;

beforeEach(inject(function(_FieldsSrv_) {
  FieldsSrv = _FieldsSrv_;
}));
```

Pero los providers se inyectan al momento de cargar el módulo:


```javascript
var ConfigProvider;

beforeEach(module("app.providers", function(_ConfigProvider_) {
  ConfigProvider = _ConfigProvider_;
}));
```

### Mocks y Spies

#### $httpBackend

Incluido en [ngMock](https://docs.angularjs.org/api/ngMock) nos ayuda a testear las llamadas ajax $http.

```javascript
var $httpBackend;

beforeEach(inject(function(_$httpBackend_) {
  $httpBackend = _$httpBackend_;

  $httpBackend.when("GET", "/api/fields").respond([
      { label: "Producto", term: "producto" },
      { label: "Marca", term: "brand" }
  ]);
}));

it("should fetch authentication token", function() {
   $httpBackend.expectGET("/api/fields");
   FieldsSrv.getFieldsFromServer();
   $httpBackend.flush();
});
```

Mas información en https://docs.angularjs.org/api/ngMock/service/$httpBackend

#### spyOn

Jasmine nos ofrece el poderoso spyOn, con múltiples beneficios.

Por ejemplo, si quieres espíar un método de un servicio que es usado en el servicio que estás testeando.

```javascript
var FinancialMetricsSrv;

beforeEach(inject(function(_FinancialMetricsSrv_) {
  FinancialMetricsSrv = _FinancialMetricsSrv_;
  spyOn(FinancialMetricsSrv, 'UF').and.returnValue(25000);
}));

it("should return the total price in UF", function() {
  var order = new Order();
  order.setPrice(50000);

  expect(order.priceInUF()).toEqual(50000/2500);
});
```

Mas información en http://jasmine.github.io/2.3/introduction.html#section-Spies

### promesas

..TODO

## Ejemplos

### Provider

```javascript
describe('Config Provider', function(){
  var ConfigProvider, Config;

  beforeEach(module('app.providers', function(_ConfigProvider_) {
    ConfigProvider = _ConfigProvider_;
  }));

  beforeEach(inject(function(_Config_) {
    Config = _Config_;
  }));

  it('should configure the fields', function() {
    var fields = [{ term: 'product', label: 'Producto' }, { term: 'region', label: 'Región' }];
    ConfigProvider.setFields(fields);

    expect(Config.getFields()).toEqual(fields);
  });
});
```

### Servicios

```javascript
describe('Fields Service', function(){
  beforeEach(module('app.services'));

  var FieldsSrv, Config;

  beforeEach(inject(function(_Config_) {
    Config = _Config_;

    spyOn(Config, 'getFields').and.returnValue([
      {
        label: 'Producto',
        term: 'product'
      }
    ]);
  }));

  beforeEach(inject(function(_FieldsSrv_) {
    FieldsSrv = _FieldsSrv_;
  }));

  it('should contain a list of fields taken from config', function(){
    expect(FieldsSrv.list).toEqual(jasmine.any(Array));
    expect(FieldsSrv.list[0].label).toEqual('Producto');
    expect(FieldsSrv.list[0].term).toEqual('product');
  });
});
```

### Factories

```javascript
describe('Field Factory', function(){
  beforeEach(module('app.factories'));

  var Field;

  beforeEach(inject(function(_Field_) {
    Field = _Field_;
  }));

  it('should create a new instance', function() {
    var field = new Field('Product', 'product');

    expect(field.label).toEqual('Product');
    expect(field.term).toEqual('product');
  });
});
```
