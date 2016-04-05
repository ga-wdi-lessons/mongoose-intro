# Mongoose-Intro

## Learning Objectives

- Explain what is Mongoose
- Describe the role of Mongoose Schema and Models
- List and Describe Common Mongoose Queries
- Compare and Contrast Mongoose to Active Record
- Use Mongoose to preform CRUD functionality
- Describe how to use Validations in Mongoose
- Persist Data Using Mongoose Nested Collections

## Opening Framing (5 min)

Why are we learning MongoDB and Mongoose?

>CRUD is something that is necessary in most every application out there. We have to create, read, update, and delete information all the time.

What are the Advantages of using MongoDB (non-relational) versus PostgreSQL (relational)?


## Mongoose (10 min)

>"Let's face it, writing MongoDB validation, casting and business logic boilerplate is a drag. That's why we wrote Mongoose."

Mongoose is an ORM, that allows us to encapsulate and model our data in our applications. It gives us access to additional helpers, functions, and queries to simply and easily preform CRUD actions.

![mongoose.js](https://www.filepicker.io/api/file/KDQZV88GTIaQn6p0GagE)

---
## http://mongoosejs.com

Review example on [mongoosejs.com](http://mongoosejs.com)

```js
var mongoose = require('mongoose');
mongoose.connect('mongodb://localhost/test');

var Cat = mongoose.model('Cat', { name: String });

var kitty = new Cat({ name: 'Zildjian' });
kitty.save(function (err) {
  if (err) // ...
  console.log('meow');
});
```
## Set Up (5 min)

```bash
$ npm install mongoose --save
```

The first thing we need to do is require mongoose in our project and open a connection to the test database on our locally running instance of MongoDB:

```js
var mongoose = require('mongoose');
mongoose.connect('mongodb://localhost/test');
```

```js

var db = mongoose.connection;

// will console.log an error if db cannot connect to MongoDB
db.on('error', function(err){
  console.log(err);
});
// will console.log "Connected" if successfully connects
db.once('open', function() {
  console.log("Connected!");

  // INSERT CODE HERE!

});

```
## Mongoose Schema & Models (10 min)

What is a Mongoose Schema?
>MongoDB has a flexible schema that allows for variability between different documents in the same collection.
>This is be used to define attributes for our documents

Mongoose Schema Example:

```js

var Schema = mongoose.Schema;

var PersonSchema = new Schema({
  name: String,
  age: Number
})

```
What are Mongoose Models?
>Mongoose Models will represent documents in our database. They are essentially constructors, which will allow us to preform CRUD actions with our MongoDB Database.

```js
var Person = mongoose.model("Person", PersonSchema)
```

>The first argument is the singular name of the collection your model is for. Mongoose automatically looks for the plural version of your model name. Thus, for the example above, the model `Person` is for the `people` collection in the database. The .model() function makes a copy of schema. Make sure that you've added everything you want to schema before calling .model()!

## Mongoose CRUD

## Create

We will be constructing documents, which are instances of our model that will be saved to our MongoDB database.

## You-Do: Initial Set Up: Reminders (10 min)

```bash
$ mkdir reminders_mongoose
$ cd reminders_mongooose
$ npm install mongoose --save
```

Let's first start by defining our schema, models and creating some seed data.

Folders/files:

```bash
$ mkdir db
$ touch db/schema.js
$ touch db/seeds.js
```

Just like in `active-record` we will define the structure of our database using schemas

Instead of defining tables we'll be defining documents.

In `db/schema.js`:

```js
// requiring mongoose dependency
var mongoose = require('mongoose')

// instantiate a name space for our Schema constructor defined by mongoose.
var Schema = mongoose.Schema,
ObjectId = Schema.ObjectId

// defining schema for reminders
var ReminderSchema = new Schema({
  body: String
})

// defining schema for authors.
var AuthorSchema = new Schema({
  name: String,
  reminders: [ReminderSchema]
})

// setting models in mongoose utilizing schemas defined above
var AuthorModel = mongoose.model("Author", AuthorSchema)
var ReminderModel = mongoose.model("Reminder", ReminderSchema)
```

> Note the syntax for reminders  in the `AuthorSchema`. This signifies it will be an array of reminder documents

Next, let's define our models that will provide the interface for queries and other logic.

In `models/author.js`:

```js
require("../db/schema")
var mongoose = require('mongoose')

var AuthorModel = mongoose.model("Author")
module.exports = AuthorModel
```

In `models/reminder.js`:

```js
require("../db/schema")
var mongoose = require('mongoose')

var ReminderModel = mongoose.model("Reminder")
module.exports = ReminderModel
```

> In these model files, were setting an export to a mongoose model.

> `var AuthorModel = mongoose.model("Author")` is kind of like `class Author < ActiveRecord::Base`

Great now that we have an interface for our models, lets create a seed file so we have some data to work with in our application.

In `db/seeds.js`:

```js
var mongoose = require('mongoose')
var conn = mongoose.connect('mongodb://localhost/reminders')
var AuthorModel = require("../models/author")
var ReminderModel = require("../models/reminder")
AuthorModel.remove({}, function(err){
  console.log(err)
  })
  ReminderModel.remove({}, function(err){
    console.log(err)
    })

    var bob = new AuthorModel({name: "bob"})
    var susy = new AuthorModel({name: "charlie"})
    var tom = new AuthorModel({name: "tom"})

    var reminder1 = new ReminderModel({body: "reminder1!!"})
    var reminder2 = new ReminderModel({body: "reminder2!!"})
    var reminder3 = new ReminderModel({body: "reminder3!!"})
    var reminder4 = new ReminderModel({body: "reminder4!!"})
    var reminder5 = new ReminderModel({body: "reminder5!!"})
    var reminder6 = new ReminderModel({body: "reminder6!!"})

    var authors = [bob, susy, tom]
    var reminders = [reminder1, reminder2, reminder3, reminder4, reminder5, reminder6]

    for(var i = 0; i < authors.length; i++){
      authors[i].reminders.push(reminders[i], reminders[i+3])
      authors[i].save(function(err){
        if (err){
          console.log(err)
          }else {
            console.log("author was saved")
          }
          })
        }
        ```
## I-Do: Create Example (10 min)

```js

var jeff = new Person({name: "Jeff", age: 30});

jeff.save(function(err, person){
  if(err){
    console.log(err);
  }
  else{
    console.log(person);
  }
});

// OR

Person.create({ name: 'Jeff', age: 30 }, function (err, person) {
  if (err){
    console.log(err);
  }
  else{
    console.log(person);
  }
});
```

Let's Test it out in the Command Line!

```bash
$ mongo
$ show dbs
$ use test
$ show collections
$ db.people.find()
```

### Callback Functions

With most Mongoose Queries, we will be using a callback function, which will be called with two arguments (err, results)

>With this callback function, the query will be executed immediately and the results are then passed into the callback

## How Does Mongoose Compare to Active Record?

## Mongoose Queries

Like Active Record, Mongoose provides us with a variety of helper methods that allow us to easily retrieve documents from our database.

### Example mongoose queries
```js
// finds all documents of specified model type can also use to specify key value pair in query
Model.find({}, callback)

// finds model by id
Model.findById(someId, callback)

// removes document that match key value
Model.remove({someKey: someValue}, callback)
```


Now that we've got all of our models and seed data set. Let's start building out the reminders application. Let's update our main application file to include the dependencies we'll need.

In `index.js`:

```js
// express dependency for our application
var express = require('express')
// loads mongoose dependency
var mongoose = require('mongoose')
// loads dependency for middleware for paramters
var bodyParser = require('body-parser')
// loads dependency that allows put and delete where not supported in html
var methodOverride = require('method-override')
// loads module containing all authors contrller actions. not defined yet...
var authorsController = require("./controllers/authorsController")
// connect mongoose interfaces to reminders mongo db
mongoose.connect('mongodb://localhost/reminders')
var app = express()
// sets view engine to handlebars
app.set("view engine", "hbs")
// allows for parameters in JSON and html
app.use(bodyParser.json());
app.use(bodyParser.urlencoded({extended:true}))
// allows for put/delete request in html form
app.use(methodOverride('_method'))
// connects assets like stylesheets
app.use(express.static(__dirname + '/public'))

// app server located on port 4000
app.listen(4000, function(){
  console.log("app listening on port 4000")
})

// first route we'll define together ...
app.get("/authors", authorsController.index)
```
## Authors Index - We do

As we can see on the last line of the code above, we have just one route. It's using `authorsController.index` as a callback, but it hasn't been defined yet. Let's define it now.

```bash
$ mkdir controllers
$ touch controllers/authorsController.js
```

In `controllers/authorsController.js`:

```js
var AuthorModel = require("../models/author")
var ReminderModel = require("../models/reminder")

var authorsController = {
  index: function(req, res){
    AuthorModel.find({}, function(err, docs){
      res.render("authors/index", {authors: docs})
    })
  }
}

module.exports = authorsController;
```

> We use our model definitions from before to query our database. In our `index` action, we're doing a mongoose query for all Author documents. Upon success we render the view `authors/index` and pass in the object `{authors: docs}`. `docs` contains all the `Author` documents.

We're rendering a handlebars view that doesn't exist yet. Lets create it now as well as our layout view.

```bash
$ mkdir views
$ mkdir views/authors
$ touch views/layout.hbs
$ touch views/authors/index.hbs
```

In `views/layout.hbs`:

```
<!doctype html>
<html>
  <head>
    <link rel="stylesheet" type="text/css" href="/css/styles.css">
  </head>
  <body>
    <header>
      <h1><a href="/authors">Reminder.ly?</a></h1>
    </header>
   {{{body}}}
  </body>
</html>
```

In `views/authors/index.hbs`:

```
<a href="/authors/new">New Author</a>
{{#each authors}}
  <div class="author">
    <div class="authorName"><a href="/authors/{{_id}}">{{name}}</a></div>
    {{#each reminders}}
      <p class="reminder">{{body}}</p>
    {{/each}}
    <br>
  </div>
{{/each}}
```

> For each author, we display a link to that author's show page(which doesn't exist yet..), the author's name, as well as all the reminders that belong to them.

## Author's Show and New - You do
Create Author's Show page
- update `index.js` to include a route for an author's show page
- have it map to an action in `controllers/authorsController.js` called `show`
  - HINT!: Instead of `.find()` try `.findById`
- create a `views/authors/show.hbs` that has the following:
  - all of the author's reminders
  - a form for creating a new reminder(it's ok your form doesn't do anything right now)
  - a link to edit the author
  - a link to the authors index page

Create Author's New page
- update `index.js` to include a route for an author's new page
- have it map to an action in `controllers/authorsController.js` called `new`
- create a `views/authors/new.hbs` that has the following:
  - form to create new author(it's ok your form doesn't do anything right now)

## Creating a new author - We do
Our `views/authors/new.hbs` should look something like this:

```
<h2>Create new Author</h2>
<form action="/authors" method="post">
<label>Name:</label>
<input type="text" name="name">
<input type="submit">
</form>
```

Lets setup our `index.js` to listen for that post request.

```js
app.post("/authors", authorsController.create)
```

Finally, let's update our controller to include the create action that creates a new Author document.

In `controllers/authorsController.js`:

```js
create: function(req, res){
  var author = new AuthorModel({name: req.body.name})
  author.save(function(err){
    if (!err){
      res.redirect("authors")
    }
  })
}
```

> We're grabbing the form input using `req.body.name` and then creating a new `Author` document then saving it. Upon save it will redirect to the authors index page.

## Creating and deleting nested documents - We do

If you followed along the you do for show(`views/authors/show.hbs`), it should look something like this:

```
<h2>Reminders for {{name}}</h2>
<div>
  {{#each reminders}}
    <div class="reminders-show-each">
      <p>{{body}}</p>
    </div>
  {{/each}}
</div>

<div id="reminder-new">
  <h3>New Reminder</h3>
  <form action="/authors/{{_id}}/reminders" method="post">
    <label>Body</label>
    <input type="text" name="body">
    <input type="submit">
  </form>
</div>
```

Let's define a route and a controller action for creating reminders under an author document.

In `index.js`:

```js
app.post("/authors/:id/reminders", authorsController.addReminder)
```

In `controller/authorsController.js`:

```js
addReminder: function(req, res){
  AuthorModel.findById(req.params.id, function(err, docs){
    docs.reminders.push(new ReminderModel({body: req.body.body}))
    docs.save(function(err){
      if(!err){
        res.redirect("/authors/" + req.params.id)
      }
    })
  })
}
```

> In this callback, we're finding an author by the id in the url. Then when we find it, we're going to push a new `Reminder` document to that author and save the author with that new reminder.

Next, let's create the functionality to delete reminders as we complete them. Lets update our `views/authors/show.hbs` to include a form for deleting for each reminder:

```
{{#each reminders}}
  <div class="reminders-show-each">
    <p>{{body}}</p>
    <form method="post" action="/authors/{{../_id}}/reminders/{{_id}}?_method=delete">
        <input type="submit" value="Done!">
    </form>
  </div>
{{/each}}
```

> The actions a little weird. In order to get the id of our parent object we use `../_id`. This will also be true of any other attributes we'd want from the parent object. Another weird thing is `?_method=delete`. This was the best work around we could find using `method-override` dependency to allow for `PUT` and `DELETE` requests in html forms on express.

Now we just need set a route and a controller action.

In `index.js`:

```js
app.delete("/authors/:authorId/reminders/:id", authorsController.removeReminder)
```

In `controllers/authorsController.js`:

```js
removeReminder: function(req, res){
  AuthorModel.findByIdAndUpdate(req.params.authorId, {
    $pull:{
      reminders: {_id: req.params.id}
    }
  }, function(err, docs){
    if(!err){
      res.redirect("/authors/" + req.params.authorId)
    }
  })
}
```

> This is an alternate sytax `.findByIdAndUpdate()`. We can actually us this method and pass in an options object. In this case, we're setting a key of `$pull`. This will find the reminder by the id specified in the url(`req.params.id`) and get rid of it from the author. Then upon success of `.findByIdAndUpdate()` it redirects tot hat author's show page.

## Authors Edit/Update/destroy
Using what you've learned in class, set up edit update and destroy for authors.


## Validations


## Cliff Notes

### Example Schema

```js
var mongoose = require('mongoose')

var Schema = mongoose.Schema,
    ObjectId = Schema.ObjectId

var ChildSchema = new Schema({
  body: String
})

var ParentSchema = new Schema({
  name: String,
  children: [ChildSchema]
})

var ParentModel = mongoose.model("ParentModel", AuthorSchema)
var ChildModel = mongoose.model("ChildModel", ReminderSchema)
```

### Some Example routes

```js
app.get("/authors", authorsController.index)
app.get("/authors/new", authorsController.new)
app.post("/authors", authorsController.create)
app.get("/authors/:id", authorsController.show)
```

### Some Example Controller actions

```js
var authorsController = {
  index: function(req, res){
    AuthorModel.find({}, function(err, docs){
      res.render("authors/index", {authors: docs})
    })
  },
  new: function(req, res){
    res.render("authors/new")
  },
  create: function(req, res){
    var author = new AuthorModel({name: req.body.name})
    author.save(function(err){
      if (!err){
        res.redirect("authors")
      }
    })
  },
  show: function(req, res){
    AuthorModel.findById(req.params.id, function(err, docs){
      res.render("authors/show", docs)
    })

  }
}
```

## Nested Mongoose Collection
