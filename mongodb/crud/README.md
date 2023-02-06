### CRUD

```
@author: suktae.choi
- https://docs.mongodb.com/manual/crud/
```

### Insert

```json
db.inventory.insert()
db.inventory.insertOne()
db.inventory.insertMany()

-- example
db.inventory.insertOne({item: "canvas", size: {h: 28, w: 35.5, uom: "cm"}})
```

> If the collection does not currently exist, insert operations will create the collection.

### Find

```
db.inventory.find({status: "D"})
db.inventory.find({status: {$in: ["A", "D"]}})
```

> Although you can express this query using the [`$or`](https://docs.mongodb.com/manual/reference/operator/query/or/#op._S_or) operator, use the [`$in`](https://docs.mongodb.com/manual/reference/operator/query/in/#op._S_in) operator rather than the [`$or`](https://docs.mongodb.com/manual/reference/operator/query/or/#op._S_or) operator when performing equality checks on the same field.

- OR

```json
db.inventory.find({$or: [{status: "A"}, {qty: {$lt: 30}}]})
```

- AND

```json
db.inventory.find({status: "A"}, {qty: {$lt: 30}})
```

- AND and/or OR

```json
db.inventory.find( {
     status: "A",
     $or: [ { qty: { $lt: 30 } }, { item: /^p/ } ]
} )
```

- Embedded / Nested

```json
db.inventory.find({size: {w: 21, h: 14, uom: "cm"}})
db.inventory.find({"size.uom": "in"})
```

- Null

```json
db.inventory.find({item: null})
```

- Projection

```
Derive specific fields only
```

- Cursor

By default, the server will automatically close the cursor after 10 minutes of inactivity. Manually set is:

```json
var myCursor = db.users.find().noCursorTimeout();
```

### Update

```json
db.collection.updateOne()
db.collection.updateMany()
db.collection.replaceOne()

-- example
db.inventory.updateOne(
   {item: "paper"},
   {
     $set: {"size.uom": "cm", status: "P"},
     $currentDate: {lastModified: true}
   }
)
```

> If {upsert: true} also specified, will create document if non exist.

### Delete

```json
TBD ...
```

### Bulk Write

```json
db.collection.bulkWrite([
  {insertOne: {...}},
  {deleteOne: {...}}
])
```

- ordered: true  (default)
  - Run in serial
  - Once exception throws, remains not executed
- ordered: false
  - Run in parallel
  - Once exception throws, remains keep executed

### Retryable Write

Retryable writes allow MongoDB drivers to automatically retry certain write operations `a single time` in driver level regardless of whether retryWrites option is set to **false.**

> Operations in transactions are not individually retryable but `whole are` on it.

#### Multi-Document

| Methods     | Descriptions      |
| ----------- | ----------------- |
| #insertOne  | Supported         |
| #updateOne  | Supported         |
| #deleteOne  | Supported         |
| #replaceOne | Supported         |
| #updateMany | **Not supported** |

Bulk write operations that only consists of single-document only supported so cannot include multi-document ops, such as `updateMany`.

### Text Search

If document has text-specific values and you need to find all documents containging any of given terms

```json
db.stores.insert([
  { _id: 1, name: "Java Hut", description: "Coffee and cakes" },
  { _id: 2, name: "Burger Buns", description: "Gourmet hamburgers" },
  { _id: 3, name: "Coffee Shop", description: "Just coffee" },
  { _id: 4, name: "Clothes Clothes Clothes", description: "Discount clothing" },
  { _id: 5, name: "Java Shopping", description: "Indonesian goods" }
])

# createIndex for text-searching
db.stores.createIndex({name: "text", description: "text"})
# find documents contains text-terms
db.collection.find({$text: {$search: "java coffee shop"}})
```

But performance is not pretty much good for text-search. Use ES