# MongoDB Workshop

MongoDB is a popular, easy-to-use database. It is a NoSQL database which stores JSON-like documents. This workshop will cover the following points:

- Why databases are important
- What a SQL database is
- What MongoDB is
- How to install MongoDB
- How to create, read, update, and delete data in MongoDB
- How to use MongoDB with NodeJS


## What is MongoDB?

MongoDB is a NoSQL database which stores JSON-like documents. What do each of these mean?

- Database
- NoSQL
- JSON-like documents


### What is a database and why do we need one?

When building applications you're going to need to be able to store data for later use. For example, Twitter needs to store all its tweets, Facebook needs to store information about its users, Yelp needs to store data about restaurants.

Databases are designed to provide **efficient and easy-to-use storage and access to large amounts of persistent data**.

- **Persistent**: You can't just store all your data in memory.
	- The amount of data an application needs to store is usually orders of magnitude larger than the memory available to a system.
	- You want data to persist whenever your application stops or restarts. You don't want to lose all your data whenever your server crashes.
	- Multiple systems might want to have access to the data. of magnitude larger than the memory available to a system.
- **Efficient Storage & Access**: You could just write all this data to your disk using a text file. But there are a lot of problems with this:
	- Storage would be inefficient. Databases can compress data so it takes up less disk space. (Although some do just use flat files.)
	- Data retrieval would be inefficient. If you want to find the data for a certain user, you might have to read every file on disk to find them. Databases can index your data and use clever caching mechanisms to make sure you can access data quickly.
	- You'd have to write your own code to do all these things. Databases provide you with very powerful APIs to access your data. You can leverage the work of thousands of people who built a database so you can just focus on coding your application.

This is only a taste of what makes databases useful. Databases also solve a lot of difficult technical problems around data consistency, distribution, load balancing, backups, and more.


### How do databases fit into your application?

Here are some tips on fitting databases into your applications.

#### Three-tier application architecture

Your applications should generally be structured in three tiers:

1. **Client** - This is the presentation layer of your application, the way a user views it. It can be a website, a mobile app, a game, or other client.
2. **API** - The client talks to a web server. This web server hosts a REST API.
3. **Database** - The API talks to the database and reads or writes data as it is needed.

The key point here is that the client (i.e. your website or app) never talks directly to the database. Since talking to the database usually requires full read/write access, this would be insecure. It's better to go through a REST API.

#### Store data, not files

Databases are good at storing things like text, numbers, dates... general information. Although it's sometimes possible to store large files such as images in a database, you usually don't want to. It can be awkward and doesn't give you much benefit. It's better to store files on disk (or on a service such as AWS S3) and just store metadata about a file (such as its name, owner, and link) in the database.


### What does NoSQL mean?

MongoDB is a very modern type of database. Older databases such as MySQL and Postgres are known as _relational databases_. The Relational Model is more than 35 years old, and it's the foundation of database management systems. The database holds a set of relations, or tables.

You can think of these tables as spreadsheets. Each table is made up of columns and rows. Each row represents one record in the table.

Table: People

| id | name    | age | location      |
|---:|---------|----:|---------------|
| 1  | Ash     | 10  | Pallet Town   |
| 2  | Misty   | 10  | Cerulean City |
| 3  | Brock   | 15  | Pewter City   |

Table: Pokemon

| id | name       | type     | owner_id |
|---:|------------|----------|---------:|
| 1  | Pikachu    | Electric | 1        |
| 2  | Charmander | Fire     | 1        |
| 3  | Staryu     | Water    | 2        |
| 4  | Starmie    | Water    | 2        |
| 5  | Onyx       | Rock     | 3        |

These relational databases nearly always use a language called SQL (Structured Query Language) to retrieve information from the database. That's why relational databases are often just called _SQL databases_.

Here are some example SQL queries on the data shown above:

Query:

```sql
SELECT name, type
FROM pokemon
WHERE owner_id = 1;
```

Output:

|       name |     type |
|------------|----------|
|    Pikachu | Electric |
| Charmander |     Fire |

Query:

```sql
SELECT pokemon.name AS 'Pokemon Name', people.name AS 'Trainer Name', people.location
FROM people
LEFT JOIN pokemon
ON pokemon.owner_id = people.id
WHERE people.location IN ('Pallet Town', 'Cerulean City')
```

Output:

| Pokemon Name | Trainer Name |      location |
|--------------|--------------|---------------|
|      Pikachu |          Ash |   Pallet Town |
|   Charmander |          Ash |   Pallet Town |
|       Staryu |        Misty | Cerulean City |
|      Starmie |        Misty | Cerulean City |

You can play with this data using [this SQL Fiddle](http://sqlfiddle.com/#!9/5ab84c/14).


#### The problem with SQL: Object Relational Mapping

One of the most frustrating things about relational databases is that programming languages don't really want to work with flat tables. They want to work with rich objects. This means you have to map the tables in your relational database to the objects you use in your code. This is called _Object Relational Mappting_ (ORM). This adds complexity to your application, is time consuming, hard to maintain, and gets more difficult as your application grows. ORM has been such a pain over the years, that some programmers have even called it "[the Vietnam of Computer Science](https://blog.codinghorror.com/object-relational-mapping-is-the-vietnam-of-computer-science/)"!


#### MongoDB and NoSQL

Instead of storing data in flat tables, MongoDB stores data as JSON objects (or _documents_). This is a different approach to relational databases so it's called _NoSQL_. MongoDB also uses its own query language instead of SQL. But it's mostly called NoSQL to refer to the fact it doesn't store data as tables.

This is fantastic because now you no longer need ORM. If you us a language that has objects, you can send an object directly to the database and read it out again without having to do any complex mapping.

Relational databases are still very useful for many things. But NoSQL databases such as MongoDB now offer a quicker and easier way to add a database to your application.


### What is JSON?

_JavaScript Object Notation_ (JSON) is a simple data format based on JavaScript object literals. An object is enclosed in a pair of curly braces (`{}`) and contains a list of key-value pairs. Keys are strings. Values can be strings, numbers, booleans, arrays of values, or even other objects.
	
Here's an example JSON document:

```JSON
{
	"id": 1,
	"num": "001",
	"name": "Bulbasaur",
	"img": "http://www.serebii.net/pokemongo/pokemon/001.png",
	"type": [
		"Grass",
		"Poison"
	],
	"height": "0.71 m",
	"weight": "6.9 kg",
	"candy": "Bulbasaur Candy",
	"candy_count": 25,
	"egg": "2 km",
	"spawn_chance": 0.69,
	"avg_spawns": 69,
	"spawn_time": "20:00",
	"multipliers": [1.58],
	"weaknesses": [
		"Fire",
		"Ice",
		"Flying",
		"Psychic"
	],
	"next_evolution": [{
		"num": "002",
		"name": "Ivysaur"
	}, {
		"num": "003",
		"name": "Venusaur"
	}]
}
```

MongoDB stores data as a type of JSON called BSON (_Binary JSON_). BSON adds more types of values (such as dates, different types of integers and floats, binary data, and more). It's useful to know MongoDB documents aren't quite JSON, but in general you won't see much of a difference.

Here's an example of what the SQL database shown above might look like as a collection of objects in MongoDB:

```JSON
{
	"id": 1,
	"name": "Ash",
	"age": 10,
	"location": "Pallet Town",
	"pokemon": [
		{ "id": 1, "name": "Pikachu", "type": "Electric" },
		{ "id": 2, "name": "Charmander", "type": "Fire" }
	]
}

{
	"id": 2,
	"name": "Misty",
	"age": 10,
	"location": "Cerulean City",
	"pokemon": [
		{ "id": 3, "name": "Staryu", "type": "Water" },
		{ "id": 4, "name": "Starmie", "type": "Water" }
	]
}

{
	"id": 3,
	"name": "Brock",
	"age": 15,
	"location": "Pewter City",
	"pokemon": [
		{ "id": 5, "name": "Onyx", "type": "Rock" }
	]
}
```

We've made the decision to store Pokemon belonging to each trainer in an array on each trainer document. This kind of nesting is difficult to get in relational databases. This makes it very easy if you want to get a trainer and all their Pokemon. But you have to put some thought into how you want to structure your documents. For example, this structure might cause some issues if you need to add a Pokemon which is owned by two trainers (such as Meowth).

![Jesse and James cuddling the Pokemon Meowth](./meowth.gif)


## Installing MongoDB

To install MongoDB on your computer, visit this link: https://docs.mongodb.com/manual/installation/

Scroll down to *MongoDB Community Edition* and choose the tutorial for your operating system. Installing MongoDB actually installs several components on your system:

- Mongo Server: This is the database itself. If you want to run your MongoDB on your own machine, you'll need to run a Mongo server. The command to start the server (or _daemon_) is `mongod`.
- Mongo Shell: This is a shell for connecting to a MongoDB server. You can use this shell to send commands to the database and interact with the data. The command for starting the shell is `mongo`.
- Other tools: There are a bunch of other tools for interacting with MongoDB databases. There are tools for exporting data (`mongoexport`), importing data (`mongoimport`), monitoring databases (`mongotop`) and more.

> It's useful to install MongoDB on your own computer for development. When you deploy your application, you'll want to host MongoDB on a server in the cloud. mLab hosts MongoDB servers online so it's easy to get up and running. You can launch free 500 MB databases on mLab in seconds.


### MacOS

With [Homebrew](https://brew.sh/):

```
$ brew update
$ brew install mongodb
```

Make sure to create the `/data/db` directory for MongoDB:

```
$ mkdir -p /data/db
```


### Windows

Download the installer from here: https://www.mongodb.com/download-center#community

Add the location of MongoDB to your path:

```
set PATH="%PATH%;C:\Program Files\MongoDB\Server\3.6\bin;"
setx PATH "%PATH%;C:\Program Files\MongoDB\Server\3.6\bin;"
```

Make sure to create the `C:\data\db` directory for MongoDB:

```
md C:\data\db
```


### Linux

Depends on your linux flavor. See the instructions here: https://docs.mongodb.com/manual/administration/install-on-linux/

Make sure to create the `/data/db` directory for MongoDB:

```
$ mkdir -p /data/db
```


## Running the MongoDB Server

To start up your own MongoDB server on your machine, run the following command:

```
$ mongod
```


## Using the Mongo Shell

Run the Mongo shell to interact with your database:

```
$ mongo
```

You should see an output and command prompt similar to this (you may see a warning about access control, this can be ignored on your local machine):

```
MongoDB shell version v3.6.1
connecting to: mongodb://127.0.0.1:27017
MongoDB server version: 3.6.1
>
```

The Mongo shell is a javascript REPL which has built-in commands for communicating with a MongoDB server. Test it out by entering some JavaScript code:

```
> double = x => x*2
x => x*2
> double(4)
8
```

You can view some basic commands by entering the `help` command:


```
> help
		db.help()                    help on db methods
		db.mycoll.help()             help on collection methods
		sh.help()                    sharding helpers
		rs.help()                    replica set helpers
		help admin                   administrative help
		help connect                 connecting to a db help
		help keys                    key shortcuts
		help misc                    misc things to know
		help mr                      mapreduce

		show dbs                     show database names
		show collections             show collections in current database
		show users                   show users in current database
		show profile                 show most recent system.profile entries with time >= 1ms
		show logs                    show the accessible logger names
		show log [name]              prints out the last segment of log in memory, 'global' is default
		use <db_name>                set current database
		db.foo.find()                list objects in collection foo
		db.foo.find( { a : 1 } )     list objects in foo where a == 1
		it                           result of the last line evaluated; use to further iterate
		DBQuery.shellBatchSize = x   set default number of items to display on shell
		exit                         quit the mongo shell
```

Let's test out some of these commands. List all the databases on the MongoDB server with this command:

```
> show dbs
admin		0.000GB
local		0.000GB
```

A new MongoDB server only has two databases, the `admin` and `local` databases. These are for use by MongoDB. You generally don't want to use these.

Each _database_ is made up of _collections_ of _documents_. The current database is available in the `db` varibale. By default it is set to `test`:

```
> db
test
```

This doesn't show up when you run `show dbs` because there aren't any documents in the `test` database yet. Databases and collections are created lazily when you insert documents. Let's insert a document into the `people` collection in the `test` database:

```
> db.people.insertOne({ name: "Misty", age: 10, location: "Cerulean City" })
{
    "acknowledged" : true,
    "insertedId" : ObjectId("5a861440faab325a3b3326a6")
}
```

This will create the `test` database, the `people` collection inside the `test` database, and the document for Misty inside the `people` collection.

See the current databases:

```
> show dbs
admin   0.000GB
local   0.000GB
test    0.000GB
```

And the collections in the current database:

```
> show collections
people
```

To show all the documents in a collection, use the `find` method:

```
> db.people.find()
{ "_id" : ObjectId("5a861440faab325a3b3326a6"), "name" : "Misty", "age" : 10, "location" : "Cerulean City" }
```

Collections are properties on the `db` object. Each collection has methods for manipulating documents in the collection: finding, inserting, updating, deleting, counting, and more. You can see a full list of methods on a collection by running `db.people.help()`.

Here's another method to count the number of documents in a collection:

```
> db.people.count()
1
```

### The `_id` field

You'll notice that when we inserted this document:

```
{ name: "Misty", age: 10, location: "Cerulean City" }
```

This was the document we found in the database:

```
{ "_id" : ObjectId("5a861440faab325a3b3326a6"), "name" : "Misty", "age" : 10, "location" : "Cerulean City" }
```

MongoDB adds an `_id` property to each inserted document. The value of `_id` is a unique identifier for the object called an ObjectId. The `_id` field is indexed which means it's very quick to search for documents by `_id`. You can set the `_id` manually, but it's best to let MongoDB handle it.

The ObjectId is a 24-character hex string. You'll notice it's wrapped in `ObjectId()`. This is a special BSON type. You can read more about ObjectIds in the [ObjectId documentation](https://docs.mongodb.com/manual/reference/bson-types/#objectid).


### Comparison

```
$ mongoimport -d pokedex -c pokemon --jsonArray --file pokedex.json
```

```
> use pokedex
switched to db pokedex
```

This sets the `db` variable to the `pokedex` database:

```
> db
pokedex
```


`$gt` `$lt`

` db.coll.find({ season: {$gt: 3}})`

`{$exists: true}`

`{$size: 1}`



### Update

`updateOne`

`updateMany`

### Delete

`deleteOne`

`deleteMany`


## Indexing and Performance

db.collection.createIndex({property: 1})

## Aggregation Framework

Mention briefly

## NodeJS and Mongoose

### Create an mLab database

### Create a NodeJS Project

```
$ mkdir movies
$ cd movies
$ npm init -y
$ touch app.js
$ npm install mongoose
```

```js
const mongoose = require('mongoose');
mongoose.connect('mongodb://localhost/test', { useMongoClient: true });

movieSchema = {
  name: String
}

var Movie = mongoose.model('Movie', movieSchema);

var movie = new Movie({ name: 'Zildjian' });

movie.save(function (err, doc) {
  if (err) {
    console.log(err);
  } else {
    console.log(doc);
  }
});
```
