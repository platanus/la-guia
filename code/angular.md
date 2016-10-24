Angular
=======

* Para el **manejo de autenticación** utilizamos la librería [angular-auth](https://github.com/platanus/angular-auth). Más información en este [blog post](http://cb.platan.us/rails/authentication/restmod/angular/2015/03/13/usando-angular-auth-lib-con-simple-token-authentication-gem.html)

* Para los casos más comunes en el tema de **subir archivos**, es decir, aquellos en los que tenemos un formulario donde pretendemos enviar a un servidor uno o varios documentos, mostrar una barra de progreso y entregar feedback de error a usuarios, utilizamos [angular-pallet-bundle](https://github.com/platanus/angular-pallet-bundle). Esta librería está construída sobre  [ng-file-upload](https://github.com/danialfarid/ng-file-upload) y es un bundle de: [angular-progress](https://github.com/platanus/angular-progress), [angular-doc-preview](https://github.com/platanus/angular-doc-preview) y [angular-pallet](https://github.com/platanus/angular-pallet).
Para los casos menos comunes, donde [angular-pallet-bundle](https://github.com/platanus/angular-pallet-bundle) no se adapta como necesitamos, utilizamos directamente [ng-file-upload](https://github.com/danialfarid/ng-file-upload) sirviendonos quizás, de las directivas anteriormente mencionadas.
