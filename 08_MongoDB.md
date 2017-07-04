# Persisting your data with MongoDB

## Why MongoDB?

* Relational database: SQL, strong typed, follow Schema, entry is a row in a table.
* Non-relational database (NoSQL database): not structured like a spreadsheet.

### How Mongo works

A Mongo server can have many databases on it, but there is generally one database per application.

Drivers: client libraries for certain programming languages that let a client talk to a Mongo server.

Every database will have one or more collections. Collections are like arrays, which are giant lists you can query.

Every collection will have any number of documents. Documents are stored as binary JSON (BSON).

Mongo adds a unique `_id` property to every document.

```
                                  Application
                                       |
                                       v
                                    Database
            _______________________________________________________
            |                          |                          |
            v                          v                          v
        Collection                 Collection                 Collection
   ___________________        ___________________        ___________________
   |        |        |        |        |        |        |        |        |
   v        v        v        v        v        v        v        v        v
Document Document Document Document Document Document Document Document Document
```

### Setting up Mongo

```
$ brew install mongodb
```

### Talking to Mongo from Node with Mongoose

Mongoose is an officially supported library for talking to Mongo from Node.

Mongoose provides a straight-forward, schema-based solution to modeling your application data and includes built-in type casting, validation, query building, business logic hooks and more, out of the box.
