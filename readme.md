# Mongoose-Intro

## Learning Objectives

- Explain what is Mongoose
- Describe the role of Mongoose schema and models
- List and describe common Mongoose queries
- Compare and Contrast Mongoose to Active Record
- Use Mongoose to preform CRUD functionality
- Describe how to use validations in mongoose
- Persist data using Mongoose nested collections

## Opening Framing (5 min)

Why are we learning MongoDB and Mongoose?

>CRUD is something that is necessary in most every application out there. We have to create, read, update, and delete information all the time.

What are the Advantages of using MongoDB (non-relational) versus PostgreSQL (relational)?


## Mongoose (5 min)

>"Let's face it, writing MongoDB validation, casting and business logic boilerplate is a drag. That's why we wrote Mongoose."

Mongoose is an ORM, that allows us to encapsulate and model our data in our applications. It gives us access to additional helpers, functions, and queries to simply and easily preform CRUD actions.

![mongoose.js](https://www.filepicker.io/api/file/KDQZV88GTIaQn6p0GagE)


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

## You-Do - Step 1: Initial Set Up for Reminders (5 min)

We will be creating 2 model Todo App using Mongo/Mongoose. Authors have many Reminders.

1. Fork and Clone this Repo:[https://github.com/ga-wdi-exercises/reminders_mongo]
2. Make sure to checkout locally to `mongoose` branch: `$ git checkout mongoose`

## Mongoose Install and Connection (5 min)

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
  console.log("database has been connected!");

  // INSERT CODE HERE!

});
```

Now let's run our `db/schema.js` file:

```bash
$ node db/schema.js
```

## You-Do - Step 2: Install Mongoose and Establish Connection (5 min)

Follow Instructions for `Step 2` on the `readme.md`

[See Solution]https://github.com/ga-wdi-exercises/reminders_mongo/blob/267a908faaae06ab4c35da6d671a867cf1bc6426/db/schema.js

## Mongoose Schema & Models (10 min)

What is a Mongoose Schema?
>MongoDB has a flexible schema that allows for variability between different documents in the same collection.
>This is be used to define attributes for our documents

Mongoose Schema Example:

```js

// instantiate a name space for our Schema constructor defined by mongoose.
var Schema = mongoose.Schema;

var StudentSchema = new Schema({
  name: String,
  age: Number
})

```
What are Mongoose Models?
>Mongoose Models will represent documents in our database. They are essentially constructors, which will allow us to preform CRUD actions with our MongoDB Database.

```js
// setting models in mongoose utilizing schemas defined above
var StudentModel = mongoose.model("Student", StudentSchema)
```

>The first argument is the singular name of the collection your model is for. Mongoose automatically looks for the plural version of your model name.
>Thus, for the example above, the model `Student` is for the `people` collection in the database. The .model() function makes a copy of schema. Make sure that you've added everything you want to schema before calling .model()!

## Nested Mongoose Collection (5 min)

Now, I'm going to add another model to my `db/schema.js`.

I am going to add another Schema now for Project, since I want to store Students and Project in my application.

Similar to how you might think of a one to many relationship in a relational database, we are going to mimic a student has many projects relationship.

```js
// defining schema for projects
var ProjectSchema = new Schema({
  title: String,
  unit: String
})

// adding in embedded documents for StudentSchema
var StudentSchema = new Schema({
  name: String,
  age: Number,
  projects: [ProjectSchema]

})

```
>The projects key of your ProjectSchema documents will then be an instance of DocumentArray. This is a special subclassed Array that can deal with casting, and has special methods to work with embedded documents.

[Mongoose Embedded Documents](http://mongoosejs.com/docs/2.7.x/docs/embedded-documents.html)


## You-Do: Step 3: Set Up Schema and Models (15 min)

Follow Step 3 to step up your Reminder and Author Schemas and Models

[See Solution](https://github.com/ga-wdi-exercises/reminders_mongo/blob/cfee42d3cfd0bf5f2581cc61ba712eb8e1b7777f/db/schema.js)

## I-Do: Create (10 min)

**Create Example:**

```js

var anna = new Student({name: "Anna", age: 30});

anna.save(function(err, person){
  if(err){
    console.log(err);
  }
  else{
    console.log(person);
  }
});

// OR

Student.create({ name: 'Anna', age: 30 }, function (err, person) {
  if (err){
    console.log(err);
  }
  else{
    console.log(person);
  }
});
```

**Embedded Documents Example: Add Project**

```js
var project1 = new ProjectModel({title: "memory game", unit: "JS"});

becky.projects.push(project1)
becky.save(function(err, student){
  if(err){
    console.log(err)
  }
  else{
    console.log(student + " was saved to our db!");
  }
});
```

## I-Do: Seeds Data (5 min)

We need to make sure we can connect our `schema.js` file to our `seeds.js`.

In order to do that we need to add the following in our `db/schema.js`:

```js
module.exports ={
  StudentModel: StudentModel,
  ReminderModel: ReminderModel
};
```

And add the following in `db/seeds.js`:

```js

var mongoose = require('mongoose');
var Schema = require("../db/schema.js");

var StudentModel = Schema.StudentModel
var ProjectModel = Schema.ProjectModel
```

**Seeds Data:**

In `db/seeds.js`:

```js
var mongoose = require('mongoose');
var Schema = require("../db/schema.js");

var StudentModel = Schema.StudentModel
var ProjectModel = Schema.ProjectModel

StudentModel.remove({}, function(err){
  console.log(err)
});
ProjectModel.remove({}, function(err){
  console.log(err)
});

var becky = new StudentModel({name: "becky"})
var brandon = new StudentModel({name: "brandon"})
var tom = new StudentModel({name: "tom"})

var project1 = new ProjectModel({title: "project1!!", unit: "JS"})
var project2 = new ProjectModel({title: "project2!!", unit: "Rails"})
var project3 = new ProjectModel({title: "project3!!", unit: "Angular"})
var project4 = new ProjectModel({title: "project4!!", unit: "Express"})

var students = [becky, brandon, tom]
var projects = [project1, project2, project3, project4]

for(var i = 0; i < students.length; i++){
  students[i].projects.push(projects[i], projects[i+2])
  students[i].save(function(err){
    if (err){
      console.log(err)
    }else {
      console.log("author was saved");
    }
  })
};

```
**Now Let's Test it out in the Command Line!**

```bash
$ mongo
$ show dbs
$ use test
$ show collections
$ db.students.find()
```

### Callback Functions

With most Mongoose Queries, we will be using a callback function, which will be called with two arguments (err, results)

>With this callback function, the query will be executed immediately and the results are then passed into the callback

## You-Do - Step 4: Adds Seeds Data and Create New Documents (15 min)

Follow Step 4 to create your seeds data in `db/seeds.js`.

[See Solution](https://github.com/ga-wdi-exercises/reminders_mongo/commit/9b5a93841df550516e04778066cb43bd790c11f8)

## Break (10 min)

## Mongoose Queries (10 min)

Like Active Record, Mongoose provides us with a variety of helper methods that allow us to easily retrieve documents from our database.

**Example Mongoose Queries**

```js
// finds all documents of specified model type can also use to specify key value pair in query
Model.find({}, callback)

// finds model by id
Model.findById(someId, callback)

// finds model by match a key value
Model.findOne({someKey: someValue}, callback)

// removes document that match key value
Model.remove({someKey: someValue}, callback)
```

**Find All Example**

```bash
$ mkdir controllers
$ touch controllers/studentsController.js
```

```js
var studentsController = {
  index: function(){
    StudentModel.find({}, function(err, docs){
      console.log(docs);
    });
  }
};

studentController.index();
```
**Find One Example**

```js
var studentController = {
  index: function(){
    StudentModel.find({}, function(err, docs){
      console.log(docs);
    });
  },
  show: function(req){
    StudentModel.findOne({"name": req.name}, function(err, docs){
      console.log(docs);
    });
  }
};

// studentController.index();
studentController.show({name: "becky"});
```

## You-Do: Step 5: Read (Index and Show) (10 min)

[See Solution](https://github.com/ga-wdi-exercises/reminders_mongo/commit/d51081c0bf995bbd7f47883467da1c06a03de058)

## I-Do: Update (10 min)

```js
var studentController = {
  index: function(){
    StudentModel.find({}, function(err, docs){
      console.log(docs);
    });
  },
  show: function(req){
    StudentModel.findOne({"name": req.name}, function(err, docs){
      console.log(docs);
    });
  },
  update: function(req, update){
    StudentModel.findOneAndUpdate(req, update, {new: true}, function(err, docs){
      if(err){
        console.log(err)
      }
      else{
        console.log(docs);
      }
    });
  }
};

// studentController.index();
// studentController.show({name: "becky"});
studentController.update({name: "becky"}, {name: "Sarah"});
```

## You-Do: Step-6: Update Documents (10 min)

[See Solution](https://github.com/ga-wdi-exercises/reminders_mongo/commit/d51081c0bf995bbd7f47883467da1c06a03de058)


## Creating and deleting nested documents

Let's define a route and a controller action for creating reminders under an author document.

**Creating Nested Documents:**

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
**Deleting Nested Documents**

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

## How Does Mongoose Compare to Active Record?

## Validations
