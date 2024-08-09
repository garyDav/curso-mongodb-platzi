## Connect to container

```sh
docker-compose exec mongodb bash
```

## Connect with mongosh

```sh
mongosh ""
```

```sh
show dbs
show collections
```

```sh
use("platzi_store")
db.products.find()
```

## Insert mongodb

File `insert.mongodb`

```mongodb
use("platzi_store")

db.product.insertOne({
  name: "Product 1",
  price: 1000
})
db.product.insertOne({
  name: "Product 2",
  price: 100
})

// Cambiar el _id
db.product.insertOne({
  id: 1,
  name: "Product 1",
  price: 100
})

// Al insertar
```

## Validate por BSON Types

En MongoDB, al usar la validación de esquemas con $jsonSchema, puedes aplicar una variedad de reglas y propiedades de validación a los diferentes tipos de datos soportados. Aquí te muestro una lista de las propiedades y reglas de validación más comunes para cada tipo de dato.

__Propiedades Generales para Todos los Tipos__

* `bsonType`: Define el tipo de dato de un campo (e.g., `string`, `object`, `array`, `date`, `bool`, `objectId`, `int`, `double`).

* `required`: Una lista de campos que deben estar presentes en el documento.

* `enum`: Especifica un conjunto de valores válidos para un campo.

* `description`: Proporciona una descripción del campo.

__Propiedades para `string`__

* `minLength`: Longitud mínima de la cadena.

* `maxLength`: Longitud máxima de la cadena.

* `pattern`: Expresión regular que la cadena debe coincidir.

* `format`: Formatos específicos de cadena como `date-time`, `email`, `ipv4`, etc. (MongoDB no soporta nativamente todos los formatos de JSON Schema).

__Propiedades para `number`__ (e.g., `int`, `double`)

* `minimum`: Valor mínimo permitido.

* `maximum`: Valor máximo permitido.

* `exclusiveMinimum`: Si `true`, el valor debe ser mayor que el minimum (no igual).

* `exclusiveMaximum`: Si `true`, el valor debe ser menor que el maximum (no igual).

* `multipleOf`: El valor debe ser un múltiplo del número especificado.

__Propiedades para `array`__

* `minItems`: Número mínimo de elementos en el array.

* `maxItems`: Número máximo de elementos en el array.

* `uniqueItems`: Si `true`, todos los elementos del array deben ser únicos.

* `items`: Define el tipo de datos y las reglas para los elementos del array.`

__Propiedades para object__

* `properties`: Define los campos internos del objeto y sus reglas.

* `minProperties`: Número mínimo de propiedades requeridas.

* `maxProperties`: Número máximo de propiedades permitidas.

* `required`: Define los campos que deben estar presentes en el objeto.

* `additionalProperties`: Controla si se permiten o no propiedades adicionales no definidas en `properties`.

__Propiedades para `bool`__

* No hay propiedades específicas adicionales para booleanos más allá de `enum`, `bsonType`, `description`.

__Propiedades para `date`__

* `bsonType`: Establecer como `date`.

* No hay propiedades específicas adicionales para fechas, pero se pueden combinar con otras validaciones como `enum`, `description`, etc.`

__Propiedades para `objectId`__

* `bsonType`: Establecer como `objectId`.

* No hay propiedades específicas adicionales para `objectId` más allá de `enum`, `description`.

__Ejemplo Completo con Diferentes Tipos de Datos:__

```json
{
  "$jsonSchema": {
    "bsonType": "object",
    "required": ["name", "age", "email", "address", "tags", "createdAt"],
    "properties": {
      "name": {
        "bsonType": "string",
        "minLength": 2,
        "maxLength": 100,
        "description": "Name must be a string between 2 and 100 characters"
      },
      "age": {
        "bsonType": "int",
        "minimum": 18,
        "maximum": 100,
        "description": "Age must be an integer between 18 and 100"
      },
      "email": {
        "bsonType": "string",
        "pattern": "^[\w-\.]+@([\w-]+\.)+[\w-]{2,4}$",
        "description": "Must be a valid email address"
      },
      "address": {
        "bsonType": "object",
        "required": ["street", "city"],
        "properties": {
          "street": {
            "bsonType": "string",
            "description": "Street must be a string"
          },
          "city": {
            "bsonType": "string",
            "description": "City must be a string"
          }
        }
      },
      "tags": {
        "bsonType": "array",
        "minItems": 1,
        "maxItems": 10,
        "uniqueItems": true,
        "items": {
          "bsonType": "string",
          "description": "Each tag must be a string"
        },
        "description": "Tags must be an array of strings with 1 to 10 unique items"
      },
      "createdAt": {
        "bsonType": "date",
        "description": "Created at must be a date"
      },
      "userId": {
        "bsonType": "objectId",
        "description": "Must be a valid ObjectId and reference a user document"
      }
    }
  }
}
```

1. Validación de Email:

```json
{
  "email": {
    "bsonType": "string",
    "pattern": "^[\\w-\\.]+@([\\w-]+\\.)+[\\w-]{2,4}$",
    "description": "Debe ser una dirección de correo electrónico válida"
  }
}
```

2. Validación de Número de Teléfono (formato internacional):

```json
{
  "phoneNumber": {
    "bsonType": "string",
    "pattern": "^\\+?[1-9]\\d{1,14}$",
    "description": "Debe ser un número de teléfono válido en formato internacional"
  }
}
```

3. Validación de URL:

```json
{
  "website": {
    "bsonType": "string",
    "pattern": "^(https?:\\/\\/)?([\\da-z\\.-]+)\\.([a-z\\.]{2,6})([\\/\\w \\.-]*)*\\/?$",
    "description": "Debe ser una URL válida"
  }
}
```

4. Validación de Código Postal (para un formato específico como el de EE.UU.):

```json
{
  "postalCode": {
    "bsonType": "string",
    "pattern": "^\\d{5}(-\\d{4})?$",
    "description": "Debe ser un código postal válido de EE.UU. (formato 12345 o 12345-6789)"
  }
}
```

5. Validación de Fecha en Formato ISO 8601:

```json
{
  "dateString": {
    "bsonType": "string",
    "pattern": "^\\d{4}-\\d{2}-\\d{2}T\\d{2}:\\d{2}:\\d{2}(\\.\\d{1,3})?Z$",
    "description": "Debe ser una cadena de fecha en formato ISO 8601 (por ejemplo, 2023-08-07T14:48:00.000Z)"
  }
}
```

6. Creación de Índice Único para una Propiedad string:

```js
db.users.createIndex({ email: 1 }, { unique: true })
```