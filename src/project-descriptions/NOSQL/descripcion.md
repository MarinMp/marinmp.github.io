# NoSQL
Se documentarán prácticas utilizando bases de datos NoSQL.

---

## Ejercicios Prácticos

### Comandos básicos de MongoDB

```sql
-- Descripción: Muestra las bases de datos disponibles en MongoDB.

show dbs
```

```sql
-- Descripción: Cambia a la base de datos "tienda". La base de datos se crea automáticamente al insertar el primer documento.

use tienda
```

```sql
-- Descripción: Inserta un documento en la colección "productos" con nombre, precio y stock.

db.productos.insertOne({
    nombre: "Camiseta",
    precio: 19.99,
    stock: 50
})
```

```sql
-- Descripción: Consulta todos los documentos almacenados en la colección "productos".

db.productos.find()
```
### Operaciones CRUD básicas

#### Insertar documentos

```sql
-- Descripción: Inserta un documento en la colección "clientes".

db.clientes.insertOne({
    nombre: "María García",
    email: "maria@example.com",
    edad: 32,
    ciudad: "Madrid"
})
```

```sql
-- Descripción: Inserta múltiples documentos en la colección "clientes".

db.clientes.insertMany([
    {
        nombre: "Carlos",
        edad: 28,
        ciudad: "Barcelona"
    },
    {
        nombre: "Ana",
        edad: 41,
        ciudad: "Valencia"
    },
    {
        nombre: "Luis",
        edad: 19,
        ciudad: "Sevilla"
    }
])
```
#### Leer documentos

```sql
-- Descripción: Consulta todos los documentos de la colección "clientes".

db.clientes.find()
```

```sql
-- Descripción: Obtiene el primer documento que coincida con la ciudad especificada.

db.clientes.findOne({
    ciudad: "Madrid"
})
```

```sql
-- Descripción: Filtra documentos por edad.

db.clientes.find({
    edad: 28
})
```

```sql
-- Descripción: Filtra documentos utilizando múltiples condiciones (AND implícito).

db.clientes.find({
    edad: 28,
    ciudad: "Barcelona"
})
```
#### Actualizar documentos

```sql
-- Descripción: Actualiza la ciudad de un cliente específico.

db.clientes.updateOne(
    {
        email: "maria@example.com"
    },
    {
        $set: {
            ciudad: "Toledo"
        }
    }
)
```

```sql
-- Descripción: Incrementa la edad de todos los clientes en 1.

db.clientes.updateMany(
    {},
    {
        $inc: {
            edad: 1
        }
    }
)
```

```sql
-- Descripción: Reemplaza completamente un documento excepto el campo _id.

db.clientes.replaceOne(
    {
        _id: ObjectId("65f1a2b3...")
    },
    {
        nombre: "María Pérez",
        edad: 33,
        ciudad: "Toledo"
    }
)
```
#### Borrar documentos

```sql
-- Descripción: Elimina un documento de la colección "clientes".

db.clientes.deleteOne({
    email: "maria@example.com"
})
```

```sql
-- Descripción: Elimina múltiples documentos que cumplan una condición.

db.clientes.deleteMany({
    edad: {
        $lt: 18
    }
})
```

```sql
-- Descripción: Elimina todos los documentos de la colección "clientes".

db.clientes.deleteMany({})
```
#### Insertar o actualizar

```sql
-- Descripción: Inserta un documento si no existe o lo actualiza si ya existe.

db.clientes.updateOne(
    {
        email: "nuevo@example.com"
    },
    {
        $set: {
            nombre: "Nuevo Cliente",
            edad: 25
        }
    },
    {
        upsert: true
    }
)
```
### Taller MQL Mongo (sample_airbnb)

```sql
-- Descripción: Inserta un nuevo listing con nombre, resumen, tipo de propiedad y precio.

db.listingsAndReviews.insertOne({
    name: "Cozy Cabin Retreat",
    summary: "Cabaña acogedora ideal para descansar en la naturaleza",
    property_type: "Cabin",
    price: 150.00
})
```

```sql
-- Descripción: Inserta un listing con un objeto address anidado incluyendo país y ciudad.

db.listingsAndReviews.insertOne({
    name: "Departamento en Santiago",
    summary: "Alojamiento cómodo en zona céntrica",
    property_type: "Apartment",
    price: 120.00,
    address: {
        country: "Chile",
        market: "Santiago"
    }
})
```

```sql
-- Descripción: Inserta múltiples listings en una sola operación.

db.listingsAndReviews.insertMany([
    {
        name: "Studio de Arte Urbano",
        price: 80.00
    },
    {
        name: "Loft Minimalista",
        price: 110.00
    }
])
```

```sql
-- Descripción: Consulta el nombre y precio de todos los listings ubicados en Brasil.

db.listingsAndReviews.find(
    {
        "address.country": "Brazil"
    },
    {
        _id: 0,
        name: 1,
        price: 1
    }
)
```

```sql
-- Descripción: Encuentra listings con más de 5 camas y muestra el nombre y número de reseñas.

db.listingsAndReviews.find(
    {
        beds: {
            $gt: 5
        }
    },
    {
        _id: 0,
        name: 1,
        number_of_reviews: 1
    }
)
```

```sql
-- Descripción: Encuentra listings cuyo tipo de propiedad sea House o Condominium.

db.listingsAndReviews.find({
    property_type: {
        $in: [
            "House",
            "Condominium"
        ]
    }
})
```

```sql
-- Descripción: Encuentra listings que tengan exactamente 20 amenities.

db.listingsAndReviews.find({
    amenities: {
        $size: 20
    }
})
```

```sql
-- Descripción: Muestra los 10 listings más caros proyectando nombre, precio y país.

db.listingsAndReviews.find(
    {},
    {
        _id: 0,
        name: 1,
        price: 1,
        "address.country": 1
    }
)
.sort({
    price: -1
})
.limit(10)
```

```sql
-- Descripción: Actualiza un listing por nombre y, si no existe, lo inserta utilizando upsert.

db.listingsAndReviews.updateOne(
    {
        name: "Mansión de Prueba"
    },
    {
        $set: {
            name: "Mansión de Prueba",
            price: 1500.00,
            address: {
                country: "United States"
            }
        }
    },
    {
        upsert: true
    }
)

