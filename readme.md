# Mongoose-Intro

## Learning Objectives

- Explain what is Mongoose
- Describe the role of Mongoose schema and models
- Use Mongoose to preform CRUD functionality
- List and describe common Mongoose queries
- Persist data using Mongoose Embedded Documents
- Describe how to use validations in mongoose

## Opening Framing (5 min)

Why are we learning MongoDB and Mongoose?

>CRUD is something that is necessary in most every application out there. We have to create, read, update, and delete information all the time.

> MongoDB stores Binary JSON (BJSON), so it translates very well to a JS application when using Express!

When would we use MongoDB (non-relational) versus PostgreSQL (relational)?

>HINT theres not always a right answer!

![mongoose.js](https://www.filepicker.io/api/file/KDQZV88GTIaQn6p0GagE)

## Mongoose (5 min)

>"Let's face it, writing MongoDB validation, casting and business logic boilerplate is a drag. That's why we wrote Mongoose."

## http://mongoosejs.com

Mongoose is an ODM (Object Data Mapping), that allows us to encapsulate and model our data in our applications. It gives us access to additional helpers, functions, and queries to simply and easily preform CRUD actions.

`Mongoose` will provide us with the similar functionality to interact with `MongoDB` and `Express` as `Active Record` did with `PostgreSQL` and `Rails`

Review example on [mongoosejs.com](http://mongoosejs.com)

```js
var mongoose = require('mongoose');
mongoose.connect('mongodb://localhost/test');

var Cat = mongoose.model('Cat', { name: String });

var kitty = new Cat({ name: 'Zilda' });
kitty.save(function (err) {
  if (err) // ...
  console.log('meow');
});
```

## You-Do - Step 1: Initial Set Up for Reminders (5 min)

We will be creating a 2 model Todo App using Mongoose and MongoDB. In this case, we will be creating two collections Authors and Reminders. Authors will have many Reminders

1. Fork and Clone this Repo: https://github.com/ga-wdi-exercises/reminders_mongo
2. Make sure to checkout locally to `mongoose` branch: `$ git checkout mongoose`

[Starter Code](https://github.com/ga-wdi-exercises/reminders_mongo/tree/mongoose)

[Solution Code](https://github.com/ga-wdi-exercises/reminders_mongo/tree/mongoose-solution)

## I-Do - Students & Projects Example: Mongoose and Connection Set Up (5 min)


```bash
$ npm install mongoose --save
```

In order to have access to `mongoose` in our application, we need to explicitly require mongoose and open a connection to the test database on our locally

```js
var mongoose = require('mongoose');
mongoose.connect('mongodb://localhost/students');
```
>The name above `students` will be the name of the database stored in MongoDB

```js

var db = mongoose.connection;

// will console.log an error if db cannot connect to MongoDB
db.on('error', function(err){
  console.log(err);
});
// will console.log "database has been connected" if successfully connects
db.once('open', function() {
  console.log("database has been connected!");

  // INSERT CODE HERE!

});
```

Now let's run our `db/schema.js` file:

```bash
$ node db/schema.js
```

## You-Do - Step 2: Install Mongoose and Connection (5 min)

Follow Instructions for `Step 2` on the `readme.md` to require mongoose for our remindersa

[See Solution](https://github.com/ga-wdi-exercises/reminders_mongo/blob/267a908faaae06ab4c35da6d671a867cf1bc6426/db/schema.js)

## I-Do - Mongoose Schema & Models (10 min)

**What is a Mongoose Schema?**

* Everything in Mongoose starts with a Schema!

* Schemas are used to define attributes and structure for our documents

* Each Schema maps to a MongoDB collection and defines the shape of the documents within that collection

Mongoose Schema Example:

```js

// instantiate a name space for our Schema constructor defined by mongoose.
var Schema = mongoose.Schema,
  ObjectId = Schema.ObjectId

var StudentSchema = new Schema({
  name: String,
  age: Number
})

```
>Mongo will add a primary key to each object, called ObjectId, referenced as id in the data.

**What are Mongoose Models?**

* Mongoose Models will represent documents in our database. They are essentially constructors, which will allow us to preform CRUD actions with our MongoDB Database.

* Models are defined by passing a Schema instance to mongoose.model.

```js
var StudentModel = mongoose.model("Student", StudentSchema)
```
>The .model() function makes a copy of schema. Make sure that you've added everything you want to schema before calling .model()!

>The first argument is the singular name of the collection your model is for. Mongoose automatically looks for the plural version of your model name.

>The model `Student` is for the `students` collection in the database.

## Embedded Documents VS References between Collections (10 min)

Now, Let's add another model to our `db/schema.js`.

We will be adding a schema now for `Project`, since I want to create an application that tracks Students and Projects.

Similar to how you might think of a one to many relationship in a relational database, A Student will have many Projects.

**How did we describe this relationship in Active Record?**

```ruby

class Students < ActiveRecord::Base
  has_many :projects
end


class Projects < ActiveRecord::Base
  belongs_to :students
end
```

**How can we describe this similarly between Models in Mongoose?**

>We will using `embedded` or `sub` documents.

### Embedded Documents

[Embedded Documents](http://mongoosejs.com/docs/2.7.x/docs/embedded-documents.html)

What are Embedded Documents?

>Docs with schemas of their own which are elements of a parents document array,  contain all the same features as normal documents.

>The only difference is that embedded documents will not be saved individually, they are saved whenever their top-level parent document is saved.


```js
var ProjectSchema = new Schema({
  title: String,
  unit: String
})


var StudentSchema = new Schema({
  name: String,
  age: Number,
  projects: [ProjectSchema]
})

```
>The projects key of your ProjectSchema documents will be a special array that has specific methods to work with embedded documents.

>The Project Schema must be defined prior to our main Student Schema

(+) Advantages:
* Embedded Documents are easy and fast

(-) Disadvantages:
* Overhead and Scaling. Can't exceed 16MB per document

### Multiple Collections/References

[References](https://docs.mongodb.org/manual/tutorial/model-referenced-one-to-many-relationships-between-documents)

Similar to how we added a foreign key in PostgreSQL, we can add `references` to documents in other collections by storing an array of `ObjectIds` referencing document ids from another Model

```js
var ProjectSchema = new Schema({
  title: String,
  unit: String,
  students: [{type: Schema.ObjectId, ref: "Student"}]
});

var StudentSchema = new Schema({
  name: String,
  age: Number,
  projects: [ {type: Schema.ObjectId, ref: "Project"} ]
});

```

>Since we are using id to refer to other objects, we use the ObjectId type in the Mongoose definition. The ref attribute must match exactly the model name in your model definition.

(+) Advantages:
* Could offer greater flexibility with querying
* Might be a better decision for scaling

(-) Disadvantages:
* Requires more work, need to find both documents that have the references(multiple queries)

We will be using `embedded` or sub documents today in class!

## You-Do: Step 3: Set Up Schema and Models (10 min)

Follow Step 3 to step up your Reminder and Author Schemas and Models

[See Solution](https://github.com/ga-wdi-exercises/reminders_mongo/blob/cfee42d3cfd0bf5f2581cc61ba712eb8e1b7777f/db/schema.js)

## I-Do: Create Example with Students and Projects (10 min)

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

**Embedded Documents Example**

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

## Seeds Data (10 min)

We need to make sure we can connect our `schema.js` file to our `seeds.js`.

In order to do that we need to add the following in our `db/schema.js`:

```js

module.exports ={
  StudentModel: StudentModel,
  ProjectModel: ProjectModel
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
      console.log("student was saved");
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

With most Mongoose Queries, we will be using a callback function, which will be called with two arguments (err, data)

>With this callback function, the query will be executed immediately and the results or data are then passed into the callback

Why are Callbacks Necessary?

>Asynchronous JS!

## Break (10 min)

## You-Do - Step 4: Adds Seeds Data and Create New Documents (15 min)

Add seeds data to create new documents in our reminders MongoDB database!

[See Solution](https://github.com/ga-wdi-exercises/reminders_mongo/commit/9b5a93841df550516e04778066cb43bd790c11f8)


## Mongoose Queries (10 min)

[Mongoose Queries Documentation](http://mongoosejs.com/docs/api.html#query-js)

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

>We are adding a `controllers` directory and `studentsController.js` file to mimic how we might define a controller in our Express application. Similar to how our controllers helped us in Rails, we will be following similar REST conventions and using our controllers to listen for incoming requests and communication with our database as necessary

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
studentController.show({name: "becky"});
```

## You-Do: Step 5: Read (Index and Show) (10 min)

Create methods for `Index` and `Show` adding functionality to see all authors and find one author.

[See Solution](https://github.com/ga-wdi-exercises/reminders_mongo/commit/d51081c0bf995bbd7f47883467da1c06a03de058)

## I-Do: Update (5 min)

```js
  update: function(req, update){
    StudentModel.findOneAndUpdate({name: req.name}, {name: update.name}, {new: true}, function(err, docs){
      if(err){
        console.log(err)
      }
      else{
        console.log(docs);
      }
    });
  }
};

studentController.update({name: "becky"}, {name: "Sarah"});
```

>We are inserting {new: true} as an additional option. If we do not, we will get the old document as a return value, not the updated one.

## You-Do: Step-6: Update Documents (10 min)

Write an update method to edit an author in our database.

[See Solution](https://github.com/ga-wdi-exercises/reminders_mongo/commit/d51081c0bf995bbd7f47883467da1c06a03de058)


## I-Do: Delete (5 min)

```js
  destroy: function(req){
    StudentModel.findOneAndRemove(req, function(err, docs){
      if(err){
        console.log(err);
      }
      else{
        console.log(docs);
      }
    });
  }
};

studentsController.destroy({name: "bob"});
```

## Break (10 min)

## You-Do: Step-7: Delete Documents (10 min)

Write destroy methods to delete documents from our database

[See Solution](https://github.com/ga-wdi-exercises/reminders_mongo/blob/469d3c09059c60b7779a8c3a8c2fb12aefcc779a/controllers/authors.controller.js)

## Deleting Embedded Documents (5 min)

```js
removeProject: function(req, project){
  StudentModel.findOneAndUpdate(req, {
    $pull: { projects: {title: project} }
  },
  {new: true}, function(err, docs){
    if(err){
      console.log(err);
    }
    else{
      console.log(docs);
    }
  });
}
```

## You-Do: Bonus Embedded Documents

Work to Write Code to Add and Delete Reminders from an Author document

## Validations (10 min)

Mongoose contains built in validators and an option to create custom validators as well.

Validators are defined at the field level of a document and are executed when the document is being saved. If a validation error occurs, the save operation is aborted and the error is passed to the callback.

**Built in Validators:**

* `required` and `unique`: used to validate the field existence in Mongoose, which is placed in your schema on the field you want to validate.

Example: Let's say you want to verify the existence of a username field and ensure it's unique before you save the user document.

```js
var UserSchema = new Schema({
  ...
  username: {
    type: String,
    unique: true,
    required: true
  }
});

```
>This will validate the existence and uniqueness of the username field when saving the document, thus preventing the saving of any document that doesn't contain that field.

* `match`: type based validator for strings, placed in your fields for you schemas

Continuing off the above example, to validate your email field, you would need to change your UserSchema as follows:

```js
var UserSchema = new Schema({
  username: {
    type: String,
    unique: true,
    required: true
  },
  email: {
    type: String,
    match: /.+\@.+\..+/
  }
});
```

>The usage of a match validator here will make sure the email field value matches the given regex expression, thus preventing the saving of any document where the e-mail doesn't conform to a valid pattern!

* `enum`: helps to define a set of strings that are only available for that field value.

```js
var UserSchema = new Schema({
  username: {
    type: String,
    unique: true,
    required: true
  },
  email: {
    type: String,
    match: /.+\@.+\..+/
  },
  role: {
    type: String,
    enum: ['Admin', 'Owner', 'User']
  },

});
```
>By Adding in `enum`, we are adding a validation to ensure only these three possible strings are saved in the document.

**Custom Validations:**

We can also define our own validators by using the `validate property`.

This `validate property` value is typically an array consisting of a validation function.

For example, if we want to validate the length of your user's password. To do so, you would have to make these changes in your UserSchema:

```js
var UserSchema = new Schema({
  ...
  password: {
    type: String,
    validate: [
      function(password) {
        return password.length >= 6;
      },
      'Password should be longer'
    ]
  },
});
```
>This custom validator will make sure your user's password is at least six characters long or it will prevent it from saving the document

* `.pre` validation: middleware that are functions which are passed control during execution of asynchronous functions.

By using `.pre`, these are executed before validations.


```js
Userschema.pre("save", function(next) {
    var self = this;

    UserModel.findOne({email : this.email}, 'email', function(err, results) {
        if(err) {
            next(err);
        } else if(results) {
            console.warn('results', results);
            self.invalidate("email", "email must be unique");
            next(new Error("email must be unique"));
        } else {
            next();
        }
    });
});

```
## Closing (5 min)

### Quiz Questions
* How is Mongoose used to interact with MongoDB?
* What are embedded documents in Mongoose?
* Why do we create a Schema in Mongoose?
* What do we need after Mongoose queries?
* What are common built in validations for Mongoose? Why would we use them?

### Additional Resources
* [Mongoose Documention](http://mongoosejs.com/index.html)
* [Embedded Docs versus Multiple Collections](https://www.google.com/webhp?sourceid=chrome-instant&ion=1&espv=2&ie=UTF-8#q=mongoose%20embedded%20versus%20collections)
* [Active Record Versus Mongoose](active_record_comparison.md)
* [ODM For Mongo and Rails](https://github.com/mongodb/mongoid)
