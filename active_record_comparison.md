| Feature   | Active Record | Mongoose|
|---------|---------------|---------|
| Database Type| Relational | Non-Relational|
| Title | ORM | ODM |
|Language|Connects Ruby Objects to tables in database| Models JS Objects to collections in database|
|DB Data Type| SQL| BSON|
|Structure| Tables & Columns| Collections & Documents|
| Naming Conventions| Capitalized, Singular Model Name| Capitalized, Singular Model Name|
|Primary Keys| id | _id (ObjectID)|
|Find All (Index)| `Model.all` | `Model.find({}, callback)`|
|Find By Id| `Model.find(id)`|`Model.findById(Id, callback)`|
|Find By Attribute| `Model.find_by(key: "value")`| `Model.findOne({key: "value"}, callback)`|
|Create| `Model.create!(key: value)` | `Model.create({key: value})` OR `newDoc.save(callback)`|
|Update| `Model.update!(key: value)` | `Model.findOneAndUpdate({key: value}, update, callback)` OR `Model.findByIdAndUpdate(id, update, callback)`|
|Delete| `Model.destroy(id)`| `Model.findByIdAndRemove(id, callback)` |
