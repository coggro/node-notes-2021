# The Complete Node.js Developer Course (3rd Edition)

## Section 11: REST APIs and Mongoose (Task App)

### 082 - Section Intro: REST APIs and Mongoose

- We're going to create an express REST API
- This will let users execute operations using server endpoints
- We'll also explore Mongoose, a popular library when working with Node and MongoDB - setting up fields, data types, and validation

### 083 - Setting up Mongoose

- We'll make use of Mongoose to do things we don't know how to do so far - field setup, validation, data types, user association, etc.
- mongoosejs.com - docs and example code
  - Models model something in the real world and describes data in collections
  - Can then make new records of model type and use methods like `save()`
- Mongoose is an Object-Document Mapper, mapping objects to documents in the DB
- It uses mongodb driver behind the scenes, so many pieces of setup are similar
- `npm i mongoose`
- Set up project structure
- `task-manager`
  - `src`
    - `db`
      - `mongoose.js`
- `mongoose.js`

  ```js
  import mongoose from 'mongoose'

  mongoose.connect(`mongodb://127.0.0.1:27017/task-manager-api`, {
    useCreateIndex: true,
    useNewUrlParser: true,
    useUnifiedTopology: true,
  })

  const User = mongoose.model(`User`, {
    name: { type: String },
    age: { type: Number },
  })

  const me = new User({ name: `Corey`, age: 31 })
  me.save()
    .then((result) => console.log(me))
    .catch((error) => console.log(`Error!`, error))
  // { _id: 60de9d4cc0585ca0a1d5d811, name: 'Corey', age: 31, __v: 0 }
  // __v is version
  ```

### 084 - Creating a Mongoose Model

- #### Challenge: Create a model for tasks

  - Define the model with description and completed fields
  - Create a new instance of the model
  - Save the model to the database
  - Test your work

  ```js
  const Task = mongoose.model(`Task`, {
    description: { type: String },
    completed: { type: Boolean },
  })
  const task = new Task({
    description: `Give August his medicine`,
    completed: false,
  })
  task
    .save()
    .then(() => console.log(task))
    .catch((err) => console.log(`Error!`, err))
  ```

### 085 - Data Validation and Sanitization: Part 1

- We'll continue to build out users and tasks
- We'll also learn validation and sanitization
- age > 18, removing empty spaces, etc.
- Validation

  - mongoose docs -> Validation
  - All types have `required` validator
    - `required: true` on model object
  - We have a few other built-in validators, but we can also do custom validation
  - `validate(value) {}` fx can throw errors in bad cases
  - validator package is popular and feature-rich
  - Can use in `validate` fx to easily do complex validation
  - Mongoose can also do SchemaTypes
    - String, Number, Boolean, Date, Buffer, ObjectID, Array
    - Options on all schemas
      - required, default, select, validate, get, set, alias
    - String Options
      - lowercase, uppercase, trim, match, enum, minlength, maxlength
    - Number Options
      - min, max
    - Date Options
      - min, max
  - `mongodb.js`

    ```js
    const User = mongoose.model(`User`, {
      name: {
        type: String,
        required: true,
        trim: true,
      },
      age: {
        default: 0,
        type: Number,
        validate(value) {
          if (value < 0) {
            throw new Error(`Age must be a positive number`)
          }
        },
      },
      email: {
        // transforms input to lowercase
        lowercase: true,
        trim: true,
        type: String,
        required: true,
        validate(value) {
          if (!validator.isEmail(value)) {
            throw new Error(`Email is invalid`)
          }
        },
      },
    })

    const me = new User({
      name: `    Methusala         `,
      email: `    myemail@GMAIL.com`,
    })

    // {
    //   age: 0,
    //   _id: 60df3e9ce90878153c8f5af5,
    //   name: 'Methusala',
    //   email: 'myemail@gmail.com',
    //   __v: 0
    // }
    ```

### 086 - Data Validation and Sanitization: Part 2

- #### Challenge: Add a password field to user
  - Setup the field as a required string
  - Ensure the length is greater than 6
  - Trim the password
  - Ensure the password doesn't contain `password`
  - Test your work!
    ```js
    password: {
      minlength: 7,
      required: true,
      trim: true,
      type: String,
      validate(value) {
        if (value.toLowerCase().includes(`password`)) {
          throw new Error(`Password contains 'password'`)
        }
      },
    },
    ```
- #### Challenge: Customize Task model

  - Trim the description and make it required
  - Make completed optional and default it to false
  - Test your work with and without errors

  ```js
  const Task = mongoose.model(`Task`, {
    description: {
      required: true,
      trim: true,
      type: String,
    },
    completed: {
      default: false,
      type: Boolean,
    },
  })
  ```

### 087 - Structuring a REST API

- We have models for Users and Tasks and can save them in the DB
- There's plenty to figure out relating users to tasks and such
- Before we write code for it, let's talk about the structure
- REST API
  - Representational State Transfer Application Programming Interface
  - API is a set of tools that allow you to build software - super broad
  - Node has an API; Express and NPM and such also have API tools
  - REST allows clients to access and manipulate resources using a set of predefined operations
    - Resource - User, Task
    - Operation - Create, Read, Update, Destroy, Upload, etc
    - Representational - Representations of data in the DB
    - State Transfer - Server is stateless, state is on the client; client has all the information required to make and process the information in the requests
  - Send GET request, server sends back 200 & JSON, or other status
  - To create, we send POST and JSON data, and the task sends back result & 201 or other status
- The Task Resource
  - Create
    - POST /tasks // Create at least one task
  - Read
    - GET /tasks // get all tasks
    - GET /tasks/:id // get a single task
  - Update
    - PATCH /tasks/:id // Patch data on a task
  - Delete
    - DELETE /tasks/:id // Delete a single task
- What makes up an HTTP request?
  - Text-based
  - Request
    - Request: POST /tasks HTTP/1.1
    - Headers
      - Accept: application/json
      - Connection: Keep-Alive
      - Authorization: Bearer a;lkjds;ajghalksdjfh
    - Empty line
    - Request Body: {"description": "Order new drill bits"}
  - Response
    - HTTP/1.1 201 Created
    - Date: {date}
    - Server: Express
    - Content-Type: application/json
    - {"\_id": "{id}", "description": "Order new drill bits", "completed": false}

### 088 - Installing Postman

- We're gonna make a ton of HTTP requests while building this API
- Postman will be a great tool for this; basically industry standard
- Download the app (this link is surprisingly hard to find)
- Install, fire it up
- Don't actually need to create an account, but I did
- We'll get started by creating a basic request to `Get Weather` in our weather app in a new `Weather App` collection
- Requests must belong to collections, so we'll make a weather app collection
- Have a method, URL, headers, auth, etc.
- We'll use https://mead-weather-application.herokuapp.com/weather?address=Philadelphia
  - Params in Postman adjust from URL
- Can send to get response

### 089 - Resource Creation Endpoints: Part 1

- We'll set up our first REST API endpoint
- Let's focus on resource creation - new users and tasks
- npm i -D nodemon
- npm i express
- We'll create model definitions elsewhere and talk about scalable directory structures
- We'll create `src/index.js` and kick the app off from there

  ```js
  import express from 'express'

  const app = express()
  const port = process.env.PORT || 3000

  app.post(`/users`, (req, res) => {
    res.send(`testing`)
  })

  app.listen(port, () => {
    console.log(`Server is up on port ${port}.`)
  })
  ```

- We'll also set up start and dev scripts

  ```js
  "scripts": {
    "start": "node src/index.js",
    "dev": "nodemon src/index.js"
  },
  ```

- Make Task App collection in Postman and create new request

  - POST to localhost:3000/users
  - Send raw JSON in the body tab

    ```json
    {
      "name": "Corey Gross",
      "email": "corey@example.com",
      "password": "Red123!$"
    }
    ```

- We can then set the server up to parse the data so we can use it.
- `src/index.js` will get `app.use(express.json())`, which parses incoming JSON to an object to access it in request handlers

  ```js
  app.use(express.json())

  app.post(`/users`, (req, res) => {
    console.log(req.body)
    res.send(`testing`)
  })
  ```

- Nothing new comes through in the response, but we can see the data sent in in the console
- We can then create a new user
- First, we should restructure our models out of `mongoose.js` and only leave the code there that's necessary to connect to the database
- We'll create the `src/models` directory and add a file for each model we want to create, starting with user.js
- We'll paste the entire User model into the file and add the imports for `mongoose` and `validator`
- We can remove `validator` from `mongoose.js` and the example creation and saving code we wrote. The Task model can stay for now.
- My mongoose setup tasks some tweaks to work with imports
  - The connection will be set to `const db = mongoose.createConnection(...)` and exported from the `mongoose.js` file
  - It will then need to be imported anywhere it's used, like models and `index.js`
- `index.js`

  ```js
  app.post(`/users`, (req, res) => {
    const user = new User(req.body)
    user
      .save()
      .then(() => {
        res.send(user)
      })
      // Find status codes at httpstatuses.com
      // We'll use 200 for things going well, 400 for bad client data, and 500 for bad server action
      .catch((e) => {
        res.status(400).send(e)
      })
  })
  ```

- And that's our first REST API route!
- We'll do tasks in the next video

### 090 - Resource Creation Endpoints: Part 2

- #### Challenge: Set up the Task creation endpoint
  - Create a separate file for the task model and load into index.js
  - Create the task creation endpoint to handle success and error
  - Test the endpoint from postman with good and bad data
  ```js
  app.post(`/tasks`, (req, res) => {
    const task = new Task(req.body)
    task
      .save()
      .then(() => {
        res.send(task)
      })
      .catch((e) => {
        res.status(400).send(e)
      })
  })
  ```
- We should change our 200 codes to 201s for Created

### 091 - Resource Reading Endpoints: Part 1

- Now that we can create, let's read!
- We'll have two: one with a list of multiple items, and another that targets just one
- We'll do users here
- Be sure to check out https://mongoosejs.com/docs/queries.html for info on the query fxs
- We'll use find() and findOne() here, along with deleteOne() and updateOne() later.
- index.js
  ```js
  app.get(`/users`, (req, res) => {
    User.find({})
      .then((users) => {
        res.status(200).send(users)
      })
      .catch((e) => {
        res.status(500).send()
      })
  })
  ```
- Express gives us route parameters, like `/:id`, that gives us values from the URL params on `req.params`
  ```js
  app.get(`/users/:id`, (req, res) => {
    const _id = req.params.id
    User.findById(_id)
      .then((user) => {
        if (!user) {
          return res.status(404).send()
        }
        res.status(200).send(user)
      })
      .catch((e) => {
        res.status(500).send(e)
      })
  })
  ```

### 092 - Resource Reading Endpoints: Part 2

- Let's create task reading endpoints
- #### Challenge: Set up the task reading endpoints

  - Create an endpoint for fetching all tasks
  - Create an endpoint for fetching a task by its id
  - Set up new requests in Postman and test your work

  ```js
  app.get(`/tasks`, (req, res) => {
    Task.find({})
      .then((tasks) => {
        res.status(200).send(tasks)
      })
      .catch((e) => {
        res.status(500).send()
      })
  })

  app.get(`/tasks/:id`, (req, res) => {
    const _id = req.params.id
    Task.findById(_id)
      .then((task) => {
        if (!task) {
          return res.status(404).send()
        }
        res.status(200).send(task)
      })
      .catch((e) => {
        res.status(500).send()
      })
  })
  ```

### 093 - Promise Chaining

- Let's take a break to cover the advanced Promise Chaining concept
- We do one async thing so far, but what about when we want to do one async and then another?
- Let's move to `playground`
- We can make an async adding function ourselves...

  ```js
  const add = (a, b) => {
    return new Promise((resolve, reject) => {
      setTimeout(() => {
        resolve(a + b)
      }, 2000)
    })
  }

  add(1, 2)
    .then((sum) => {
      console.log(sum)
    })
    .catch((e) => {
      console.log(e)
    })
  ```

- If we want to use the sum in another async `add` call, we can just put it in the `then` block... but then it gets way nested and complex and duplicated code.
- We can promise chain instead.
- If we return a promise from our `then` block, we can put another `then` on the end and continue. The catch at the end will catch all errors in the whole block.
  ```js
  add(1, 2)
    .then((sum) => {
      console.log(sum)
      return add(sum, 4)
    })
    .then((sum) => {
      console.log(sum)
    })
    .catch((e) => {
      console.log(e)
    })
  ```
- Now that we get it, let's use it in our tasks.
- We can play in a `task-manager/playground` directory to get access to our other methods
- Let's change the age of a user and then get all users with that same age.

  - We may want to use `findByIdAndUpdate()` or `findOneAndUpdate()`, since they return the found item as well as finding it

  ```js
  import User from '../src/models/user.js'

  User.findByIdAndUpdate(`60df3e9ce90878153c8f5af5`, { age: 1 })
    .then((user) => {
      console.log(user)
      return User.countDocuments({ age: 1 })
    })
    .then((count) => {
      console.log(count)
    })
    .catch((e) => {
      console.log(e)
    })
  ```

- The deprecation warning we get is something behind the scenes with the mongo driver. We don't need to worry about it - it just hasn't been addressed in Mongoose yet - but we can set `useFindAndModify` to `false` on the connection if it's annoying
- So this is promise chaining. Cool.

### 094 - Promise Chaining Challenge

- #### Challenge: Mess around with promise chaining

  - Create promise-chaining-2.js
  - Load in mongoose and task model
  - Remove a given task by id
  - Get and print the total number of incomplete tasks
  - Test your work!

  ```js
  import Task from '../src/models/task.js'

  Task.findByIdAndDelete(`60e44f527450292a57116731`)
    .then((task) => {
      console.log(task)
      return Task.countDocuments({ completed: false })
    })
    .then((count) => {
      console.log(count)
    })
    .catch((e) => {
      console.log(e)
    })
  ```

### 095 - Async/Await

- We should cover this before adding more functionality to the task manager
- Async/Await is maybe the biggest improvement to JS as long as Andrew's been coding with it
- This isn't a whole new thing - it's just a small set of tools that make it easy to work with promises
- This will play a major role in the task-manager app; it'll be used on every route
- Let's start with dummy async tasks in `playground/010-async-await.js`

  ```js
  // Not async
  const doWork = () => {}

  console.log(doWork())
  // undefined
  ```

  ```js
  // async
  const doWork = async () => {}

  console.log(doWork())
  // Promise { undefined }
  //
  // Promise fulfilled with value undefined
  //
  // async functions return a promise fulfilled by the value returned by the
  // function
  ```

  ```js
  // async with return value
  const doWork = async () => {
    return 'Andrew'
  }

  console.log(doWork())
  // Promise { 'Andrew' }
  ```

  ```js
  // async handled with promise structure
  const doWork = async () => {
    return 'Andrew'
  }

  doWork()
    .then((result) => {
      console.log(`result: ${result}`)
    })
    .catch((e) => {
      console.log(`e: ${e}`)
    })
  ```

  ```js
  // Async throwing error
  const doWork = async () => {
    throw new Error(`Something went wrong!`)
    return 'Andrew'
  }

  doWork()
    .then((result) => {
      console.log(`result: ${result}`)
    })
    .catch((e) => {
      console.log(`e: ${e}`)
    })
  // e: Error: Something went wrong!
  //
  // Throwing an error is equivalent to rejecting the promise
  ```

- The other half is `await`
  - It can only be used in `async` functions
  - We'll borrow add from `009-promises.js`
  - `async/await` doesn't change how Promises are written; you just change how you work with them
  - Libraries don't need to rewrite either way
- Inside `doWork()`, let's call `add()` a few times

  ```js
  const doWork = async () => {
    // Can assign to a var instead of promise flow for syntactical benefit
    const sum = await add(1, 99)
  }
  doWork()
    .then((result) => {
      console.log(`result: ${result}`)
    })
    .catch((e) => {
      console.log(`e: ${e}`)
    })
  // Waits the 2s, returns 100
  // Can use code that looks synchronous to do async tasks
  ```

  ```js
  const doWork = async () => {
    const sum = await add(1, 99)
    const sum2 = await add(sum, 50)
    const sum3 = await add(sum2, 3)
    return sum3
  }
  // Runs in 6s, so it takes a while
  // But it's easier to read/write
  ```

  ```js
  const add = (a, b) => {
    return new Promise((resolve, reject) => {
      setTimeout(() => {
        // Let's add a rejection case
        if (a < 0 || b < 0) {
          return reject(`Numbers must be non-negative`)
        }
        resolve(a + b)
      }, 2000)
    })
  }

  const doWork = async () => {
    const sum = await add(1, 99)
    // This will throw an error after ~4s
    const sum2 = await add(sum, -50)
    const sum3 = await add(sum2, 3)
    return sum3
  }
  ```

### 096 - Async/Await: Part 2

- Let's put it into practice and do a challenge or two.
- Let's get back into task-manager
- We'll convert `promise-chaining.js` to async/await, do `promise-chaining-2.js` as a challenge, then we'll do some async/await in `index.js` to clean up the code.

  ```js
  cconst updateAgeAndCount = async (id, age) => {
    const user = await User.findByIdAndUpdate(id, { age })
    const count = await User.countDocuments({ age })
    return count
  }

  updateAgeAndCount(`60df447cafd67319b93f897e`, 2)
    .then((count) => {
      console.log(count)
    })
    .catch((e) => {
      console.log(e)
    })

  ```

- #### Challenge: Use async/await

  - Create `deleteTaskAndCount()` as an async function
  - Accept id of the task to remove
  - Use await to delete task and count up incomplete tasks
  - Return the count
  - Call the function and attach then/catch to log results
  - Test your work!

  ```js
  const deleteTaskAndCount = async (id) => {
    // We could also just do await Task.find...
    const task = await Task.findByIdAndDelete(id)
    const count = await Task.countDocuments({ completed: false })
    return count
  }

  deleteTaskAndCount(`60e44ebd38b48f298e7ebfeb`)
    .then((count) => {
      console.log(count)
    })
    .catch((e) => {
      console.log(e)
    })
  // This was my own little experiment
  console.log(`Immediately after function call`)
  // OUTPUT:
  // Immediately after function call
  // 3
  ```

### 097 - Integrating Async/Await

- We're going to async/await our existing routes
- `index.js`

  ```js
  app.post(`/users`, async (req, res) => {
    const user = new User(req.body)
    user
      .save()
      .then(() => {
        res.status(201).send(user)
      })
      .catch((e) => {
        res.status(400).send(e)
      })
  })

  // BECOMES

  app.post(`/users`, async (req, res) => {
    const user = new User(req.body)
    try {
      await user.save()
      res.status(201).send(user)
    } catch (e) {
      res.status(400).send(e)
    }
  })

  ---

  app.get(`/users`, async (req, res) => {
    User.find({})
      .then((users) => {
        res.status(200).send(users)
      })
      .catch((e) => {
        res.status(500).send()
      })
  }
  // BECOMES
  app.get(`/users`, async (req, res) => {
    try {
      const users = await User.find({})
      res.status(200).send(users)
    } catch (e) {
      res.status(500).send()
    }
  })
  ```

- I honestly think I might like the promise syntax better...

  ```js
  app.get(`/users/:id`, (req, res) => {
    const _id = req.params.id
    User.findById(_id)
      .then((user) => {
        if (!user) {
          return res.status(404).send()
        }
        res.status(200).send(user)
      })
      .catch((e) => {
        res.status(500).send(e)
      })
  })
  // BECOMES
  app.get(`/users/:id`, async (req, res) => {
    const _id = req.params.id
    try {
      const user = await User.findById(_id)

      if (!user) {
        return res.status(404).send()
      }

      res.status(200).send(user)
    } catch (e) {
      res.status(500).send(e)
    }
  })
  ```

- Test everything with Postman
- #### Challenge: Refactor task routes to async/await
  - Refactor task routes to use async/await
  - Test all routes in Postman
- NOTE: The function(s) that generate(s) the promise(s) need(s) to be INSIDE the try block.

### 098 - Resource Updating Endpoints: Part I

- Let's set up some update endpoints
  ```js
  app.patch(`/users/:id`, async (req, res) => {
    const _id = req.params.id
    // Only allow changes to existing properties
    const updates = Object.keys(req.body)
    const allowedUpdates = [`name`, `email`, `password`, `age`]
    const isValidOperation = updates.every((item) =>
      allowedUpdates.includes(item)
    )
    if (!isValidOperation) {
      return res.status(400).send({ error: `Invalid updates!` })
    }
    // Update user
    try {
      // Takes id, body (updated properties), and options (return new user, run validators on update)
      const user = await User.findByIdAndUpdate(_id, req.body, {
        new: true,
        runValidators: true,
      })
      if (!user) {
        return res.status(404).send()
      }
      res.status(200).send(user)
    } catch (e) {
      res.status(400).send(e)
    }
  })
  ```

### 099 - Resource Updating Endpoints: Part 2

- We'll set up the endpoint for updating tasks by ID
- #### Challenge: Allow for task updates
  - Set up the route handler
  - Send error if unknown updates
  - Attempt to update the task
    - Handle task not found
    - Handle validation errors
    - Handle success
  - Test your work!
  ```js
  app.patch(`/tasks/:id`, async (req, res) => {
    const _id = req.params.id
    const updates = Object.keys(req.body)
    const allowedUpdates = [`description`, `completed`]
    const isValidOperation = updates.every((item) =>
      allowedUpdates.includes(item)
    )
    if (!isValidOperation) {
      return res.status(400).send({ error: `Invalid updates!` })
    }
    try {
      const task = await Task.findByIdAndUpdate(_id, req.body, {
        new: true,
        runValidators: true,
      })
      if (!task) {
        return res.status(404).send()
      }
      res.status(200).send(task)
    } catch (e) {
      res.status(400).send(e)
    }
  })
  ```

### 100 - Resource Deleting Endpoints

- Fairly straightforward at this point

  ```js
  ...
  app.delete(`/users/:id`, async (req, res) => {
    const _id = req.params.id
    try {
      const user = await User.findByIdAndDelete(_id)
      if (!user) {
        return res.status(404).send()
      }
      res.status(200).send(user)
    } catch (e) {
      res.status(500).send(e)
    }
  })
  ...
  app.delete(`/tasks/:id`, async (req, res) => {
    const _id = req.params.id
    try {
      const task = await Task.findByIdAndDelete(_id)
      console.log(task)
      if (!task) {
        return res.status(404).send()
      }
      res.status(200).send(task)
    } catch (e) {
      res.status(500).send(e)
    }
  })
  ...
  ```

### 101 - Separate Route Files

- Everything is currently in `index.js`
- Maybe we should split these up into separate route files...
- We could split them up into Users and Tasks
- We'll set up multiple Express routers and set them up as we need
- So we instead create routers and export them from `src/routers/user.js` and `src/routers/task.js`
- We just set `const router = new express.Router()` and add the endpoints with `router.{method}` instead of `app.{method}`
- We can then export them and incorporate them into the app using `app.use(userRouter)` and `app.use(taskRouter)`
