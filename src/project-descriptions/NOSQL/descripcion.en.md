# NoSQL
Practices using NoSQL databases will be documented.

---

## Practical Exercises

### Basic MongoDB Commands

```sql
-- Description: Displays the databases available in MongoDB.

show dbs
```

```sql
-- Description: Switches to the "tienda" database. The database is automatically created when the first document is inserted.

use tienda
```

```sql
-- Description: Inserts a document into the "productos" collection with name, price, and stock.

db.productos.insertOne({
    nombre: "Camiseta",
    precio: 19.99,
    stock: 50
})
```

```sql
-- Description: Retrieves all documents stored in the "productos" collection.

db.productos.find()
```
### Basic CRUD Operations

#### Insert Documents

```sql
-- Description: Inserts a document into the "clientes" collection.

db.clientes.insertOne({
    nombre: "María García",
    email: "maria@example.com",
    edad: 32,
    ciudad: "Madrid"
})
```

```sql
-- Description: Inserts multiple documents into the "clientes" collection.

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
-- Description: Retrieves all documents from the "clientes" collection.

db.clientes.find()
```

```sql
-- Description: Retrieves the first document that matches the specified city.

db.clientes.findOne({
    ciudad: "Madrid"
})
```

```sql
-- Description: Filters documents by age.

db.clientes.find({
    edad: 28
})
```

```sql
-- Description: Filters documents using multiple conditions (implicit AND).

db.clientes.find({
    edad: 28,
    ciudad: "Barcelona"
})
```
#### Update Documents

```sql
-- Description: Updates the city of a specific client.

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
-- Description: Increments the age of all clients by 1.

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
-- Description: Completely replaces a document except for the _id field.

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
#### Delete Documents

```sql
-- Description: Deletes a document from the "clientes" collection.

db.clientes.deleteOne({
    email: "maria@example.com"
})
```

```sql
-- Description: Deletes multiple documents that meet a condition.

db.clientes.deleteMany({
    edad: {
        $lt: 18
    }
})
```

```sql
-- Description: Deletes all documents from the "clientes" collection.

db.clientes.deleteMany({})
```

#### Insert or Update

```sql
-- Description: Inserts a document if it does not exist or updates it if it already exists.

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
### MQL Mongo Workshop (sample_airbnb)

```sql
-- Description: Inserts a new listing with name, summary, property type, and price.

db.listingsAndReviews.insertOne({
    name: "Cozy Cabin Retreat",
    summary: "Cozy cabin ideal for relaxing in nature",
    property_type: "Cabin",
    price: 150.00
})
```

```sql
-- Description: Inserts a listing with a nested address object including country and market.

db.listingsAndReviews.insertOne({
    name: "Apartment in Santiago",
    summary: "Comfortable accommodation in a central area",
    property_type: "Apartment",
    price: 120.00,
    address: {
        country: "Chile",
        market: "Santiago"
    }
})
```

```sql
-- Description: Inserts multiple listings in a single operation.

db.listingsAndReviews.insertMany([
    {
        name: "Urban Art Studio",
        price: 80.00
    },
    {
        name: "Minimalist Loft",
        price: 110.00
    }
])
```

```sql
-- Description: Retrieves the name and price of all listings located in Brazil.

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
-- Description: Finds listings with more than 5 beds and displays the name and number of reviews.

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
-- Description: Finds listings whose property type is House or Condominium.

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
-- Description: Finds listings that have exactly 20 amenities.

db.listingsAndReviews.find({
    amenities: {
        $size: 20
    }
})
```

```sql
-- Description: Displays the 10 most expensive listings showing name, price, and country.

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
-- Description: Updates a listing by name and inserts it if it does not exist using upsert.

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
```