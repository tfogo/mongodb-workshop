# MongoDB Hackathon Workshop

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


## Create an mLab database

See the [documentation here](http://docs.mlab.com/) for a step by step guide for creating a database. Remember to create your database user and password on the Users tab for your database. You'll need to create these to connect to your database.


## Querying

Exit the Mongo shell by running the exit command:

```
> exit
bye
```

You should now be back on your command line. Let's download some data about Pokemon. On Mac/Linux you can run this command:

```
$ curl https://raw.githubusercontent.com/tfogo/mongodb-workshop/master/pokedex.json -o pokedex.json
```

On Windows, you may not have `curl` installed, so you can right click on this link and select _Save Link As_: [pokedex.json](https://raw.githubusercontent.com/tfogo/mongodb-workshop/master/pokedex.json).

Now import the data into the `pokemon` collection in the `pokedex` database using the `mongoimport` command:

```
$ mongoimport -h <host>:<port> -d <database> -c <collection> -u <user> -p <password> --jsonArray --maintainInsertionOrder --file pokedex.json
2018-02-16T12:44:37.951-0800    connected to: localhost
2018-02-16T12:44:37.968-0800    imported 151 documents
```

Start the Mongo shell again:

```
$ mongo
MongoDB shell version v3.6.1
connecting to: mongodb://ds012345@mlab.com:12345
MongoDB server version: 3.4.10
>
```

The `db` variable is already set to your database:

```
> db
pokedex
```

Now we can view the documents in the `pokemon` collection:

```
> db.pokemon.find().pretty()
```

The `.pretty()` method will output the documents in an easily readable format. There are 151 documents in the collection, but you'll notice the shell only printed 20 of them, then said this:

```
Type "it" for more
```

This is because queries don't return documents directly, they return a cursor. Then you iterate over the cursor to return documents. This is so that, if you have a million documents in a collection, you don't return them all at once which could seriously hamper your application's performance. The Mongo shell iterates over cursors in 20 document batches. Most drivers in programming languages have batches of 101 documents. The batch size can vary depending on a lot of things, it's just something to be aware of.

Iterate to the next batch of documents by typing `it`:

```
> it
```

You can pass a _filter document_ to the `find()` method to filter the documents by their properties. This command will return all documents where the `name` is `"Charizard"`:

```
> db.pokemon.find({ name: "Charizard" }).pretty()
```

This will return all documents where the `height` is `"1.70 m"`:

```
> db.pokemon.find({ height: "1.70 m" }).pretty()
```

You can see that this returns 6 Pokemon (Charizard, Golduck, Victreebel, Rapidash, Dewgong, and Articuno).

You can also drill into document properties using the dot notation that is common in JavaScript objects (See [here](https://docs.mongodb.com/manual/core/document/#dot-notation) for more information.)

There are many special _query operators_ which start with `$` signs that can be used to locate documents. Here we use the `$gt` operator to find all Pokemon with a weight more than `"90.0 kg"`:

```
> db.pokemon.find({ weight: { $gt: "90.0 kg" } }).pretty()
```

This returns just two Pokemon (Charizard and Rapidash).

To find the rarest Pokemon, we can use the `$lt` operator. This will return the Pokemon that have a spawn chance of less than 0.1%:

```
> db.pokemon.find({ spawn_chance: { $lt: 0.001 } }).pretty()
```

There are only 6 Pokemon that are this rare (Ditto, Articuno, Zapdos, Moltres, Mewtwo, and Mew).

There are operators for nearly any type of filtering you may want to do. Read the [Query and Projection Operators](https://docs.mongodb.com/manual/reference/operator/query/) documentation to learn about what's available. Here are a few examples you may find useful:

Find all Pokemon that have only one type (checks that the `type` array has only 1 element):

```
> db.pokemon.find({ type: { $size: 1 } }).pretty()
```

Find all Pokemon that are in their final evolution state (checks for the documents that don't have a `next_evolution` property):

```
> db.pokemon.find({ type: { $all: ["water"] } }).pretty()
```

Find all the Pokemon that are both water type and less than 1.00 m tall:

```
> db.pokemon.find({ $and: [ { type: "Water" }, { height: { $lt: "1.00 m" } } ] }).pretty()
```


### Counting

You can count the number of documents that match a _filter document_ by using the `count` method on a collection:

```
> db.pokemon.count()
151
> db.pokemon.count({ height: "1.70 m" })
6
```

Or, instead of appending `.pretty()` to `find` queries, you can append `.count()`:

```
> db.pokemon.find({ height: "1.70 m" }).count()
6
```


### Projection

If you don't want to return all the properties in a document, you can use a _projection document_ as the second argument to the `find` command to only show certain arguments. For example, to only show the name and weight of Pokemon 1.7m tall:

```
> db.pokemon.find({ height: "1.70 m" }, { name: true, weight: true }).pretty()
{
        "_id" : ObjectId("5a8744754c24cb5c863ed309"),
        "name" : "Charizard",
        "weight" : "90.5 kg"
}
{
        "_id" : ObjectId("5a8744754c24cb5c863ed33a"),
        "name" : "Golduck",
        "weight" : "76.6 kg"
}
{
        "_id" : ObjectId("5a8744754c24cb5c863ed34a"),
        "name" : "Victreebel",
        "weight" : "15.5 kg"
}
{
        "_id" : ObjectId("5a8744754c24cb5c863ed351"),
        "name" : "Rapidash",
        "weight" : "95.0 kg"
}
{
        "_id" : ObjectId("5a8744754c24cb5c863ed35a"),
        "name" : "Dewgong",
        "weight" : "120.0 kg"
}
{
        "_id" : ObjectId("5a8744754c24cb5c863ed393"),
        "name" : "Articuno",
        "weight" : "55.4 kg"
}
```

The `_id` field is always shown unless you explicitly ask for it not to be shown:

```
> db.pokemon.find({ height: "1.70 m" }, { _id: false, name: true, weight: true }).pretty()
{ "name" : "Charizard", "weight" : "90.5 kg" }
{ "name" : "Golduck", "weight" : "76.6 kg" }
{ "name" : "Victreebel", "weight" : "15.5 kg" }
{ "name" : "Rapidash", "weight" : "95.0 kg" }
{ "name" : "Dewgong", "weight" : "120.0 kg" }
{ "name" : "Articuno", "weight" : "55.4 kg" }
```


### Update

To update a single document, you can use the `updateOne` command. First, let's look at the document for Psyduck:

```
> db.pokemon.find({ name: "Psyduck"}, { name: true, height: true }).pretty()
{
        "_id" : ObjectId("5a8744754c24cb5c863ed339"),
        "name" : "Psyduck",
        "height" : "0.79 m"
}
```

Now we'll use the `$set` operator to change the height of Psyduck to 10 meters. Scary!

```
> db.pokemon.updateOne({ name: "Psyduck"}, { $set: { height: "10.0 m" } })
{ "acknowledged" : true, "matchedCount" : 1, "modifiedCount" : 1 }
```

The output tells us that the update found one document and modified one document. Let's check if the record was modified:

```
> db.pokemon.find({ name: "Psyduck"}, { name: true, height: true }).pretty()
{
        "_id" : ObjectId("5a8744754c24cb5c863ed339"),
        "name" : "Psyduck",
        "height" : "10.0 m"
}
```

If you want to update multiple documents at once, use the `updateMany` command. This will increment the average spawns of the two heaviest Pokemon by 1:

```
> db.pokemon.updateMany({ weight: { $gt: "90.0 kg" } }, { $inc: { avg_spawns: 1 } })
{ "acknowledged" : true, "matchedCount" : 2, "modifiedCount" : 2 }
```

### Delete

To delete documents, you can use `deleteOne` or `deleteMany`. For example:

```
> db.pokemon.deleteOne({ name: "Mewtwo" })
{ "acknowledged" : true, "deletedCount" : 1 }
```


## Indexing and Performance

Indexes are vital to understand if you want your database queries to be fast. If you search for documents using an unindexed field such as `name`, MongoDB will have to go through every single document on the disk and check the value of `name` to return the results.

```
> db.pokemon.find({ name: "Mew" }).explain({ executionStats: 1 })
```

The `explain` command will give you information about how a query is executed. We see in the execution stats that the `totalDocsExamined` were 150! Every document in the collection! If you have thousands or millions of records, this will get unbearably slow!

If you index a field, MongoDB will order them and put them in a data structure (a B-tree) which will make it very, very quick to find values. This index is smaller than all the documents, so it can fit in memory, making tree-traversal lightning fast compared to having to read documents from the disk.

Indexing is a deep and nuanced subject. If you start to run production apps, you should read and fully understand [the indexing documentation](https://docs.mongodb.com/manual/indexes/). For now, here's how to index a single field:

```
> db.pokemon.createIndex({ name: 1 })
{
        "createdCollectionAutomatically" : false,
        "numIndexesBefore" : 1,
        "numIndexesAfter" : 2,
        "ok" : 1
}
```

Now, running `explain` on the previous command:

```
> db.pokemon.find({ name: "Mew" }).explain({ executionStats: 1 })
```

We can see that the `totalDocsExamined` is 1. MongoDB was able to immediately pick out the answer to our query. This is much more efficient.


## Aggregation Framework

You can create even more complex queries by using the _Aggregation Framework_. These are a powerful series of operations you can perform to manipulate your data.

For example, this will select a random Pokemon:

```
> db.pokemon.aggregate([{ $sample: { size: 1 } }])
```

This will group Pokemon by their type and show how many Pokemon of each type there are:

```
> db.pokemon.aggregate([{ $group: { _id: "$type", count: { $sum: 1 } } }])
```

You can delve deeper into aggregations in the [documentation](https://docs.mongodb.com/manual/aggregation/).


## MongoDB drivers

If you want to talk to a MongoDB server from your application, you need to use a driver. There are drivers for every language you could need: Java, Python, NodeJS, PHP, C#, and dozens more.

You can find the driver for your language here: https://docs.mongodb.com/ecosystem/drivers/

Each driver will come with a guide for getting started. mLab have also compiled examples for many drivers here: https://github.com/mongolab/mongodb-driver-examples
