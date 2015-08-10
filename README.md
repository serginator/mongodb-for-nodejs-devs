MongoDB for Node.js Devs
========================

MongoDB is a non relational database, it stores json (javascript object notation, `{"key": "value"}`) documents, so its document oriented, and it's schemaless.

We have an administration shell for mongo. It's synchronous instead of working with node.js which is asynchronous.

To be able to handle mongodb connections in node.js we need a driver.

## Install mongodb

```
sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv 7F0CEB10
echo "deb http://repo.mongodb.org/apt/ubuntu "$(lsb_release -sc)"/mongodb-org/3.0 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-3.0.list
sudo apt-get update && sudo apt-get install -y mongodb-org
```

## Shell commands

* `show dbs` shows the databases
* `use demo` uses a DB
* `db.<collection>.find()` finds all documents in that collection
* `db.<colletion>.find({"a": 1})` finds all documents that have that property
* `db.<collection>.insert({"a": 1})` inserts an object in a collection, fails if it exists
* `for (var i = 0, n = 10; i < n; i++) { db.<collection>.insert({"x": i})}` will add 10 objects to the collection, `mongo` shell allows javascript in its commands, it runs on V8
* `db.<collection>.save({'b': 2})` will insert but if it exists, it will update it
* `db.<collection>.findOne()` returns just one document

## Hello World MongoDB Style

```
var mongoClient = require('mongodb').MongoClient;
mongoClient.connect('mongodb://localhost:27017/test', function(err, db) {
  if (err) {
    throw err;
  }

  db.collection('coll').findOne({}, function(err, doc) {
    if (err) {
      throw err;
    }
    console.dir(doc);
    db.close();
  });

  console.dir('called findOne');
});
```

## Hello world Express.js and Swig Style

```
var express = require('express'),
  app = express(),
  cons = require('consolidate'),
  mongo = require('mongodb'),
  MongoClient = mongo.MongoClient,
  Server = mongo.Server;

app.engine('html', cons.swig);
app.set('view engine', 'html');
app.set('views', __dirname + '/views');

var mongoClient = new MongoClient(new Server('localhost', 27017,
  { 'native_parser': true }));

var db = mongoClient.db('course');

app.get('/', function(req, res) {
  db.collection('hello_mongo_express').findOne({}, function(err, doc) {
    res.render('hello', doc);
  })
});

app.get('*', function(req, res) {
  res.send('Page not found', 404);
});

mongoClient.open(function (err, mongoClient) {
  app.listen(8080);
  console.log('Express app listening on 8080');
});

```

## mongorestore

It's a shell command that will restore a db given a folder containing another db, for example a folder like `dump/m101` containing several .bson files will generate a `m101` database with the content of those three collections.


