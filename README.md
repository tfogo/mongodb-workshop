# MongoDB Workshop

**Instructor**: Tim Fogarty - Developer Advocate, mLab

mLab hosts **over half a million** MongoDB deployments on AWS, Google, and Azure. We power databases for companies such as Lyft, Facebook, and the New York Times.

## What is MongoDB?

MongoDB is a popular database which is easy to use. It is a NoSQL database that stores JSON-like documents.

What does each of these mean?

- Databse
- NoSQL
- JSON-like documents

### What is a database and why do we need one?

When building applications you're going to need to be able to store data for later use. For example, Twitter needs to store all its tweets, Facebook needs to store information about its users, Yelp needs to store data about restaurants.
						
You could just write all this data to your disk using a text file. But there's a lot of problems with this. For one, it would quickly become very inefficient. If you want to find the data about a certain user, it would be very slow to find them. You'd also have to write your own code to do it. If you wanted to find all users based in Birmingham who visited your website in the last 3 days it would be even more difficult.
						
Databases provide an efficient way to store and access data. It provides you with a very powerful API to access your data, so your database can do the heavy lifting.

### Relational Databases

The Relational Model is more than 35 years old, and it's the foundation of database management systems. The database holds a set of relations, or tables. You can think of these as spreadsheets.

Table: People

| id | name    | age | location   |
|----|---------|-----|------------|
| 1  | Alice   | 31  | Birmingham |
| 2  | Bob     | 24  | London     |
| 3  | Charlie | 45  | Paris      |

Table: Tweets

| id | text      | poster_id | Time |
|----|-----------|-----------|------|
| 1  | Hello     | 2         | 3:45 |
| 2  | #pancakes | 1         | 4:30 |
| 3  | Tweeting! | 2         | 4:37 |

```
SELECT name, location
FROM People
WHERE age > 30;
```

### MongoDB



### JSON/BSON



## Installing MongoDB

To install MongoDB on your computer, visit this link: https://docs.mongodb.com/manual/installation/

Scroll down to *MongoDB Community Edition* and choose the tutorial for your operating system.

### MacOS

#### With [Homebrew](https://brew.sh/)

```
$ brew update
$ brew install mongodb
```

#### Running MongoDB

```
$ mkdir -p /data/db
```

### Windows

Download the installer from here: https://www.mongodb.com/download-center#community

### Linux

Depends on your linux flavour. See the instructions here: https://docs.mongodb.com/manual/administration/install-on-linux/

### Running the MongoDB Server

For Mac/Linux:

```
$ mongod &
```

For Windows:

```
$ 'C:\Program Files\MongoDB\Server\3.4\bin\mongod.exe'
```

### Running the MongoDB Shell

For Mac/Linux:

```
$ mongo
```

For Windows:

```
$ 'C:\Program Files\MongoDB\Server\3.4\bin\mongo.exe'
```

## CRUD

```
> help
```

```
> show dbs
```

```
> use codeschool
```

```
> show collections
```

```
> db.coll.insert({ name: "", age: 27 })
```

```
> db.coll.find()
```

### The `_id` field

### Comparison

```
$ mongoimport -d school -c movies --jsonArray --file data.json
```

`$gt` `$lt`

` db.coll.find({ season: {$gt: 3}})`

`{$exists: true}`

`{$size: 1}`



### Update

updateOne

updateMany

### Delete

deleteOne

deleteMany


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
