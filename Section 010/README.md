# The Complete Node.js Developer Course (3rd Edition)

## Section 10: MongoDB and Promises (Task App)

### 070 - Section Intro: Databases and Advanced Asynchronous Development

- Databases and MongoDB!
- Starting task manager REST API
- User auth, DB storage, file uploads, email notifications, and more!
- Set up DB, connect to DB, explore CRUD

### 071 - MongoDB and NoSQL Databases

- Let's do some housekeeping - close editors, get out of web-server, clear everything
- We'll be using mongodb, originally launched in 2009
- We could use MySQL or PostGRES, but Mongo is nice
- Mongo is NoSQL - different than a SQL DB
- We get an npm module to easily read/write from the DB
- SQL vs NoSQL
  - DB === DB
  - Tables !== Collections
  - Rows/Records !== Documents
  - Columns !== Fields

### 072 - Installing MongoDB on MacOS and Linux

- Download the tgz and extract the folder
- Rename to mongodb and copy it to home
- Create a mongodb-data directory in the same folder
- Run executable in the bin dir
  - `/home/corey/mongodb/mongod --dbpath=/home/corey/mongodb-data`
- Loads of output, and then it sits and waits

### 073 - Installing MongoDB on Windows

- Skipped because Windows sucks and I don't need to waste my own time

### 074 - Installing Database GUI Viewer

- We should install a GUI admin tool
- Get Robo 3T
  - MacOS gets a DMG
  - Linux gets a tar.gz
- Install, open, and create connection to localhost:21017
- Can test connection by opening a shell and entering db.version() to get db version

### 075 - Connecting and Inserting Documents

- We'll connect to mongodb from node and add records from a script
- mongodb.com has docs for drivers
- Create `task-manager` dir, `npm init`, and `npm i mongodb`
- Add `mongodb.js` file to experiment in.

  ```js
  import mongodb from 'mongodb'
  const MongoClient = mongodb.MongoClient

  const connectionURL = `mongodb://127.0.0.1:27017`,
    databaseName = `task-manager`

  MongoClient.connect(
    connectionURL,
    { useNewUrlParser: true, useUnifiedTopology: true },
    (err, client) => {
      if (err) {
        console.log(err)
        return console.log(`Unable to connect to database`)
      }
      console.log(`Connected correctly`)
    }
  )
  ```

- Node process stays open until you close it
- We can insert a document by replacing the console.log with insertion code

  ```js
  // Don't need to create the db, mongo will do it for us
  const db = client.db(databaseName)
  // Same with the collection, and then we insert a record like an
  // object with the insertOne() function on the collection.
  db.collection(`users`).insertOne({
    name: `Corey`,
    age: `31`,
  })
  ```

- Can then see the record in Robo3T
  - Shows `_id` that we didn't add and values we added

### 076 - Inserting Documents

- Let's learn to insert docs better with insertOne and insertMany
- insertOne is async with an optional callback

  - Takes `error` and `result`
  - `result` gets our data and the unique ID
  - We'll use a conditional to handle an error
  - `result.ops` is an array of all the documents inserted using the insert function with their \_id fields
  - `insertOne` returns a promise if no callback

  ```js
  db.collection(`users`).insertOne(
    {
      name: `Corey`,
      age: `31`,
    },
    (error, result) => {
      if (error) {
        return console.log(`Unable to insert user`)
      }

      console.log(result.ops)
    }
  )
  ```

- insertMany is async as well
  ```js
  db.collection(`users`).insertMany(
    [
      {
        name: `Rachel`,
        age: `31`,
      },
      {
        name: `August`,
        age: `5`,
      },
    ],
    (error, result) => {
      error
        ? console.log(`unable to insert documents`)
        : console.log(result.ops)
    }
  )
  ```
- #### Challenge: Insert 3 tasks into a new tasks collection
  - Use insertMany to insert three documents
    - description (string), completed (boolean)
  - Set up the callback to handle error or print ops
  - Run the script
  - Refresh the db and view data
  ```js
  db.collection(`tasks`).insertMany(
    [
      { description: `Take out the trash`, completed: false },
      { description: `Do laundry`, completed: false },
      { description: `Play video games`, completed: false },
    ],
    (error, result) => {
      error
        ? console.log(`Unable to insert documents`)
        : console.log(result.ops)
    }
  )
  ```
- This knocks out Create. Next, Read!

### 077 - The ObjectID

- We've inserted documents and seen the \_id/objectID, the automatically generated unique identifier
- Let's talk about the value inside.
- It's different than SQL dbs, which are usually ID'd by incrementing integers
- These are GUID, Globally Unique Identifiers. This allows MongoDB to be distributed over several servers all creating records without duplicating IDs
- We can also generate IDs before inserting them into the DB
- We'll comment out insertMany and get ObjectID from mongoDB
  ```js
  const { MongoClient, ObjectID } = mongodb
  ...
  const id = new ObjectID()
  console.log(id)
  //60de272dc9cd973b7e7d6fc0
  ```
- This includes a timestamp and other data, not completely randomly generated
  - 12-byte value
    - 4 bytes of seconds in the Unix epoch
    - 5 bytes of random value
    - 3-byte counter starting with a random value
  - We can get the timestamp using getTimestamp() on the id
- We can set the ID ourselves by specifying \_id on the record
- Generally better to have Mongo create it for us
- ObjectIDs are stored as an object, and are displayed as a string generated from a function
  - `id.id` gets raw binary info
  - `id.toHexString()` gets the hex string representation
  - This makes it easier to visualize for humans
- We need to know this to fetch items by \_id

### 078 - Querying Documents

- Let's learn to read docs from the DB (Read)
- Collection.find() and .findOne()
- .findOne()
  ```js
  // collection(`users`).findOne({search criteria object})
  db.collection(`users`).findOne(
      { name: `Rachel`, age: 999 },
      (error, user) => {
        error ? console.log(`Unable to fetch`) : console.log(user)
      }
    )
  }
  // { _id: 60de1f67e561c5364df53bfc, name: 'Rachel', age: '31' }
  ```
  - Returns null if no users match the criteria
  - findOne returns the first one it finds
  - Can use \_id to find a specific item using `new ObjectID(string)`
- find()

  - Returns a cursor, not an object or array, and not async?
  - Cursor.toArray()

    ```js
    // Get an array of user data
    db.collection(`users`)
      .find({ age: 31 })
      .toArray((error, users) => {
        console.log(users)
      })
    // [
    //   { _id: 60de0e7ebc2e47280f725f25, name: 'Corey', age: 31 },
    //   { _id: 60de1dd44397af348c726998, name: 'Corey', age: 31 },
    //   { _id: 60de1f67e561c5364df53bfc, name: 'Rachel', age: 31 },
    //   { _id: 60de27eea4924c3c5c26fa30, name: 'Joe', age: 31 }
    // ]
    // The cursor is valuable because you can also just get metadata
    db.collection(`users`)
      .find({ age: 31 })
      .count((error, count) => {
        console.log(count)
      })
    // 4
    ```

- #### Challenge: Use find and findOne with tasks
  - Use findOne to fetch the last task by its id (print doc to console)
  - Use find to fetch all tasks that are not completed (print docs to console)
  - Test your work!
  ```js
  db.collection(`tasks`).findOne(
    { _id: new ObjectID(`60de20e534aa98378dc4d769`) },
    (error, users) => {
      console.log(users)
    }
  )
  db.collection(`tasks`)
    .find({ completed: false })
    .toArray((error, tasks) => {
      console.log(tasks)
    })
  ```

### 079 - Promises

- Let's talk about promises, which are designed to solve issues with callbacks
- They build on callbacks as an enhancement
- We'll work on `playground/8a-callbacks.js` and `playground/8b-promises.js`
- `playground/8a-callbacks.js`

  ```js
  const doWorkCallback = (callback) => {
    setTimeout(() => {
      // callback(`This is my error!`, undefined)
      callback(undefined, [1, 4, 7])
    }, 2000)
  }

  doWorkCallback((error, result) => {
    error ? console.log(error) : console.log(result)
  })
  ```

  - Familiar pattern
  - Order of callback args matters (err, res)
  - Conditional logic necessary in callback

- `playground/8b-promises.js`
  ```js
  const
  ```
  - Promise is declared as new Promise()
  - Takes a function as an argument with `resolve` and `reject` args
    - `(resolve, reject) => {}`
  - Calling `resolve()` inside the promise equates to success, and `reject()` to failure
  - They can take in a value to pass to `then()` (resolve) or `.catch()` (reject)
- Promises are clearly advantageous
  - resolve/reject give clearer semantics; callback requires args to be set up
  - Callbacks are a single fuction that handles all cases; Promises have separate functions for each potential result
  - You can't call both resolve/reject or call one multiple times, so it's easier to not mess up
  - Vocab: Promises are `pending` until they're `fulfilled` or `rejected`

### 080 - Updating Documents

- Back to `/task-manager/mongodb.js`
- Let's switch up someone's name in `users`
- We'll target a user by \_id
- updateOne and updateMany exist for updating values; update should not be used
- `updateOne(filter, update, options, callback)`
  ```js
  db.collection(`users`)
    .updateOne(
      // search filter
      {
        _id: new ObjectID('60de0e7ebc2e47280f725f25'),
      },
      // update with $set operator
      { $set: { name: `Theo` } }
    )
    // chain right onto it
    .then((result) => {
      console.log(result)
    })
    .catch((error) => {
      console.log(error)
    })
  ```
  - Returns a promise if no callback is passed
  - update arg is an object that requires update operators, like `$set`, set to objects with new props/values
  - It will return a promise
  - `modifiedCount` and `matchedCount` in the result are `0` or `1` for `updateOne`, depending on if a document was updated or even found
  - We can chain everything on the `db.collection()` call
- Update Operators
  - https://docs.mongodb.com/manual/reference/operator/update/
  - $set, $unset, $sort, $increment, etc.
  - $set is most likely
  - $inc can increment/decrement a field by a value
- #### Challenge: Use updateMany to complete all tasks
  - Check the documentation for `updateMany`
  - Setup the call with the query and the updates
  - Use promise methods to setup the success/error handlers
  - Test your work!
  ```js
  db.collection(`tasks`)
    .updateMany({}, { $set: { completed: true } })
    .then((result) => {
      console.log(result)
    })
    .catch((error) => {
      console.log(error)
    })
  ```

### 081 - Deleting Documents

- D is for Delete!
- Let's delete individual documents and many at the same time!
- Used to be `remove`, now it's `deleteMany` and `deleteOne`
- `deleteOne`
  - filter
  - options (not needed)
  - callback (promise instead)
  ```js
  db.collection(`users`)
    .deleteMany({ age: 31 })
    .then((result) => {
      console.log(result)
    })
    .catch((error) => {
      console.log(error)
    })
  ```
- #### Challenge: Use deleteOne to remove a task
  - Grab the description for the task you want to remove
  - Set up the call with the query
  - Use promise methods to set up the success/error handles
  - Test your work
  ```js
  db.collection(`tasks`)
    .deleteOne({ description: `Take out the trash` })
    .then((result) => {
      console.log(result)
    })
    .catch((error) => {
      console.log(error)
    })
  ```
- Now that we're comfortable with MongoDB and promises, we can start using them with Express for the REST API
