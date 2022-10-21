# Getting Started

El 90 % de los proyectos Platanus, son aplicaciones web alojadas en Github que comienzan con un proyecto Ruby on Rails.
Este muy probablemente no será una SPA y usará Vue.js + Tailwind CSS para enriquecer la experiencia de usuario.
En algún momento del desarrollo conectaremos algunas partes del frontend con el back utilizando una API REST y probaremos nuestro código con RSpec.
Además, romperemos un poco el MVC que propone Rails con patrones como observers, comandos y servicios para estructurar y manejar mejor el código.
Cuando los procesos se vuelvan pesados usaremos jobs para ejecutar en background y, a la hora del deploy, nos serviremos de [Circle CI](https://circleci.com/) para ayudarnos en el proceso de integración continua.
Al final, nuestra aplicación se servirá en internet con la ayuda de [heroku](https://www.heroku.com/).

Si estás pensando en postular para trabajar en Platanus o simplemente sientes curiosidad de nuestro stack, te recomendamos mirar los recursos listados debajo.
El resto de la guía contiene información específica de cómo hacemos las cosas aquí. Por esto, si no estás familiarizado con nuestras herramientas, este es un buen lugar para partir:

## Recursos

### Github

* [Git and GitHub for Beginners - Crash Course](https://www.youtube.com/watch?v=RGOj5yH7evk)

* [Git Tutorial for Beginners: Command-Line Fundamentals](https://www.youtube.com/watch?v=HVsySz-h9r4)

### Ruby/Rails

* [Ruby Programming Language](https://www.youtube.com/watch?v=t_ispmWmdjY): introducción básica a Ruby.

* [Rails 5: The Tour](https://youtu.be/OaDhY_y8WTo): introducción básica a Rails. Está hecho a partir de Rails 5 (aunque ya vamos por la versión 6) pero en esencia es lo mismo y es material oficial.

* [Ruby on Rails Guides](https://guides.rubyonrails.org/active_job_basics.html): guía oficial de Ruby on Rails. Toda la guía es excelente pero quizás es un poco grande. De aquí al menos revisaría: [Getting Started with Rails](https://guides.rubyonrails.org/getting_started.html), [Active Record Basics](https://guides.rubyonrails.org/active_record_basics.html), [Active Record Migrations](https://guides.rubyonrails.org/active_record_migrations.html), [Action Controller Overview](https://guides.rubyonrails.org/action_controller_overview.html), [Layouts and Rendering in Rails](https://guides.rubyonrails.org/layouts_and_rendering.html) y [Action Mailer Basics](https://guides.rubyonrails.org/action_mailer_basics.html).

### Vue

* [Official Documentation](https://vuejs.org/guide/): documentación oficial de Vue.

* [Learn Vue.js - Full Course for Beginners](https://www.youtube.com/watch?v=4deVCNJq3qc)

### API Rest

* [What is REST API?](https://www.youtube.com/watch?v=rtWH70_MMHM)

* [REST API concepts and examples](https://www.youtube.com/watch?v=7YcW25PHnAA)

### RSpec

* [Testing with RSpec](https://www.youtube.com/watch?v=71eKcNxwxVY)

* [RSpec Documentation](https://relishapp.com/rspec/rspec-core/docs)

* [Better Specs](http://www.betterspecs.org/): buenas y malas prácticas en RSpec.

* [Mocking con RSpec](https://blog.platan.us/mocking-con-rspec-4c2b2689cf93): quizás esto es un poco avanzado pero es bueno tener conocimiento sobre mocks, stubs, etc. ya que, probablemente, este tema es el más difícil de incorporar sobre testing.

### Tailwind

* [Getting started](https://tailwindcss.com/docs/installation/): documentación oficial.

* [Tailwind CSS Crash Course](https://www.youtube.com/watch?v=UBOj6rqRUME)

### Patrones

* [Services, Commands y otros poderosos patrones en Rails](https://blog.platan.us/services-commands-y-otros-poderosos-patrones-en-rails-27c2d3aa7c2e): blog post de Platanus donde explicamos para qué se usan los comandos, servicios y alguna cosa más.

### Jobs

* [Active Job Basics](https://guides.rubyonrails.org/active_job_basics.html): guía oficial de Rails sobre ActiveJob.

* [Drifting Ruby - Background Jobs with Sidekiq](https://www.youtube.com/watch?v=CStZg8ql9Vs): video que explica de manera muy concisa el funcionamiendo de sidekiq con Rails. Algunas cosas de configuración han cambiado pero igual es un buen video para entender como trabaja.


