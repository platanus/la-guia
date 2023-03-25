# Google BigQuery

Google BigQuery es un [*data warehouse*](https://en.wikipedia.org/wiki/Data_warehouse) *serverless. *En esencia, es una base de datos SQL, externa a la infraestructura que se suele llevar en nuestros proyectos, y está diseñado para funcionar con grandes volúmenes de datos. Sus integraciones son súper sencillas, por lo que permite tener soluciones para *analytics* rápidamente.

## ¿Cuando/por qué usar BigQuery?

Podríamos considerar usar BigQuery en un proyecto en los siguientes casos:

* Se tiene mucha información transaccional, a la que se quiere tener acceso para hacer analytics.

* Se quieren aprovechar herramientas que tengan una fácil integración con big query (como data studio)

* Se quiere evitar sobrecargar la base de datos operacional con consultas para analytics que pueden ser pesadas.

* Si el producto es B2B podría ser buena idea tener una base de datos individual para cada cliente, que incluya la información relacionada con su empresa y se le pueda facilitar al cliente para que pueda integrar con las herramientas de BI que estime convenientes.

Esto ya que las ventajas de usar Google BigQuery son:

* Manejar grandes volúmenes de datos con alta velocidad.

* Acceder a una interfaz en la consola de Google Cloud que permite hacer consultas usando SQL

* Integrarse con herramientas de *analytics* y BI de manera sencilla (como por ejemplo Google DataStudio).

* Al utilizar una base de datos externa para *analytics* podemos evitar la carga que implicaría sobre nuestra base de datos en heroku las consultas que se requieran para obtener estadísticas más complejas.

## ¿Cómo la usamos?

### Instalación

1. **Creación de un proyecto en la consola de Google Cloud**

El primer paso para integrarse con BigQuery es tener un proyecto creado en la consola de Google Cloud.

https://cloud.google.com/resource-manager/docs/creating-managing-projects

1. **Creación de credenciales para acceder a BigQuery via api.**

Para usar la gema de BigQuery, necesitaremos una serie de credenciales que se generan creando una *Service Account *en google cloud. Debemos asegurarnos de darle permisos de BigQuery a la cuenta de servicio creada.

https://cloud.google.com/iam/docs/creating-managing-service-accounts

Posteriormente, debemos generar una key nueva para esa cuenta de servicio. Debe ser una key de tipo JSON. 

https://cloud.google.com/iam/docs/creating-managing-service-account-keys#creating

Con estos pasos, estamos listos para comenzar a configurar BigQuery en nuestro proyecto de rails.

### Uso básico

**Setup** **de la gema**

Debemos agregar la gema `google-cloud-bigquery` al gemfile.

Esta gema, automáticamente obtiene las credenciales de una variable de entorno llamada `BIGQUERY_CREDENTIALS`, por lo que para usarla debemos agregar esa variable, cuyo valor tiene que ser la key que descargamos en el archivo JSON.

Debe verse algo así:

```javascript
BIGQUERY_CREDENTIALS={"type": "service_account", "project_id": "super-proyecto-genial", "private_key_id": "some_id", "private_key": "some_private_key","client_email": "some_email","client_id": "some_id","auth_uri": "https://accounts.google.com/o/oauth2/auth","token_uri": "https://oauth2.googleapis.com/token","auth_provider_x509_cert_url": "https://www.googleapis.com/oauth2/v1/certs","client_x509_cert_url": "some_cert_url"}
```

En el lugar donde se va a usar la gema (probablemente un client), se debe hacer un `require 'google/cloud/bigquery'` y crear un objeto `Google::Cloud::Bigquery.new`. Esto obtiene las credenciales definidas en la variable de entorno automáticamente.

**Tipos de recursos**

En BigQuery manejaremos principalmente 2 tipos de recursos, datasets y tablas. 

Un dataset esta contenido dentro de un proyecto (el que usamos se define por las credenciales), y se usa para organizar y controlar acceso a tablas y vistas. 

Una tabla es lo que contiene las filas de datos que le enviaremos a BigQuery, siempre está contenido dentro de un dataset.

Dependiendo del uso, podemos tener múltiples datasets, un solo dataset, múltiples tablas o una sola tabla.

https://cloud.google.com/ruby/docs/reference/google-cloud-bigquery/latest/index.html#creating-datasets-and-tables

**Sobre la **[**desnormalización**](https://en.wikipedia.org/wiki/Denormalization)

En BigQuery no se busca mantener un esquema de base de datos que replique exactamente la que tenemos en nuestro servidor. Esto es porque las bondades de BigQuery son mejor aprovechadas si los datos con los que trabajamos están denormalizados.

No obstante, [BigQuery ofrece algunas soluciones para esto](https://cloud.google.com/bigquery/docs/best-practices-performance-nested) de manera que no sea tan complicado realizar el proceso de desnormalización.

### Ejemplo

En nuestros proyectos probablemente vamos a tener un cliente, un módulo de util que nos permita conocer el schema de la tabla y cómo desnormalizar un record, y unos cuantos jobs para la creación de datasets, creación de tablas e inserción de datos. Además, necesitaremos agregar un callback al observer del modelo que queremos registrar en bigquery.

> 💡 Para los siguientes ejemplos, supongamos que tenemos una app para realizar encuestas de cualquier tipo.

    Queremos tener una tabla para registrar la información de las respuestas, incluyendo la respuesta misma, la pregunta respondida, la encuesta asociada e información del usuario que responde.

    Queremos que esto ocurra cada vez que se crea una nueva respuesta.

    Tenemos las siguientes asociaciones entre modelos de nuestra app.

    <img src='assets/google-bigquery-1.png'/>

**Cliente**

```ruby
# /app/clients/google_bigquery_client.rb
require 'google/cloud/bigquery'

class GoogleBigqueryClient
  def create_dataset(dataset_name)
    client.create_dataset(dataset_name)
  end

  def delete_dataset(dataset_name)
    dataset = client.dataset(dataset_name)
    dataset.delete
  end

  def create_table(dataset_name, table_name, schema)
    dataset = client.dataset(dataset_name)
    dataset.create_table table_name do |table|
      table.name = table_name
      table.description = "Table for #{table_name}"
      table.schema do |s|
        schema.each do |column|
          s.send(column[:type], column[:name], mode: column[:mode])
        end
      end
    end
  end

  def insert_row(dataset_name, table_name, row)
    dataset = client.dataset(dataset_name)
    table = dataset.table(table_name)
    table.insert(row)
  end

  private

  def client
    @client ||= Google::Cloud::Bigquery.new
  end
end
```

**Módulo para mantener el *****schema***** y obtener los atributos de cada *****Answer***

```ruby
# app/utils/answers_data.rb
module AnswersData
  def self.attributes
    [
      { type: :integer, name: :id, mode: :required },
      { type: :string, name: :value, mode: :required },
      { type: :integer, name: :question_id, mode: :required },
      { type: :integer, name: :user_id, mode: :required },
      { type: :string, name: :user_email, mode: :required },
      { type: :string, name: :question_name, mode: :required },
      { type: :string, name: :question_option1, mode: :required },
      { type: :string, name: :question_option2, mode: :required },
      { type: :integer, name: :survey_id, mode: :required },
      { type: :string, name: :survey_name, mode: :required }
    ]
  end

  def self.row(answer)
    {
      id: answer.id,
      value: answer.value,
      user_id: answer.user_id,
      user_email: answer.user.email,
      question_id: answer.question_id,
      question_name: answer.question.name,
      question_option1: answer.question.option_1,
      question_option2: answer.question.option_2,
      survey_id: answer.question.survey_id,
      survey_name: answer.question.survey.name
    }
  end
end
```

**Job para crear un dataset**

```ruby
# app/jobs/create_bigquery_dataset_job.rb
class CreateBigqueryDatasetJob < ApplicationJob
  def perform
    client.create_dataset('bigquery_demo')
  end

  private

  def client
    @client ||= GoogleBigqueryClient.new
  end
end
```

**Job para crear una tabla**

```ruby
# app/jobs/create_bigquery_answers_table_job.rb
class CreateBigqueryAnswersTableJob < ApplicationJob
  include AnswersData

  def perform
    client.create_table(
      'bigquery_demo', 'answers',
      AnswersData.attributes
    )
  end

  private

  def client
    @client ||= GoogleBigqueryClient.new
  end
end
```

**Job para subir una answer a BigQuery**

```ruby
class UploadAnswerToBigqueryJob < ApplicationJob
  include AnswersData

  def perform(answer_id)
    answer = Answer.find(answer_id)

    client.insert_row('bigquery_demo', 'answers', AnswersData.row(answer))
  end

  private

  def client
    @client ||= GoogleBigQueryClient.new
  end
end
```

**Observer**

```ruby
class AnswerObserver < PowerTypes::Observer
  after_create_commit :upload_to_google_bigquery
	...

  def upload_to_google_bigquery
    UploadAnswerToBigqueryJob.perform_later(object.id)
  end

	...
end
```

### Recursos útiles

https://cloud.google.com/bigquery/docs/introduction

https://github.com/googleapis/google-cloud-ruby/tree/main/google-cloud-bigquery


