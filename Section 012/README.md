# The Complete Node.js Developer Course (3rd Edition)

## Section 12: API Authentication and Security (Task App)

### 102 - Section Intro: API Authentication and Security

- We'll focus on locking down data to logged-in people
- We'll need to do secure signups and logins
- We'll also need to tie tasks to users

### 103 - Securely Storing Passwords: Part 1

- We'll start with talking about secured passwords
- Plaintext passwords are BAD
- We should store a hashed, salted password
- Hash applies a non-reversable algorithm to the password
- We'll use bcrypt via `bcryptjs`

  ```js
  import bcrypt from 'bcrypt'

  const hashFunction = async () => {
    const password = `red12345!`
    // Takes text password and # of salting rounds
    // 8 is recommended to balance between security and speed
    const hashedPassword = await bcrypt.hash(password, 8)

    console.log(password)
    console.log(hashedPassword)
  }

  hashFunction()
  // OUTPUT:
  // red12345!
  // $2b$08$5csI1toKgyMzU5peu07kp.5V1VRlKWrfnGN73qF0SYt3T8aotaOFm
  ```

- Hashing isn't reversible, like encryption... so how do people log in?
- We use bcrypt.compare()

  ```js
  const isMatch = await bcrypt.compare(password, hashedPassword)
  console.log(isMatch)
  // OUTPUT
  // true
  ```

- Compare is case-sensitive

### 104 - Securely Storing Passwords: Part 2

- Let's integrate password hashing
- The first place we need to do it is in user creation; the next one is when a user is updated, if a new password is provided
- Instead of adding functions to these routes, we'll use "middleware" with Mongoose
  - Middleware lets us run code before or after specific events
  - validate, save, remove, and init are available
- Let's restructure User to make this work.

  - The model in the second object is converted into a schema. To use middleware, we need to create the schema first and pass it in

  ```js
  ...
  // pass everything to this that used to be in the object
  const userSchema = new mongoose.Schema({...})

  const User = db.model(`User`, userSchema)
  ...
  ```

- We'll then attach some middleware before the save event on the schema

  ```js
  userSchema.pre(`save`, async function (next) {
    const user = this
    console.log(`just before saving`)
    next()
  })
  ```

- This won't fire yet because some events bypass middleware
- We have to restructure update as well by changing `findbyidandupdate` to `findById` and swapping some other code in to help it save

  ```js
  router.patch(`/users/:id`, async (req, res) => {
    ...
    try {
      // I left off the await here and had a hell of a time troubleshooting it
      const user = await User.findById(_id)

      if (!user) {
        return res.status(404).send()
      }

      // Apply updates manually
      updates.forEach((update) => (user[update] = req.body[update]))

      // Save manually to trigger hash
      await user.save()

      res.status(201).send(user)
    } catch (e) {
      res.status(400).send(e)
    }
  })
  ```

- Now let's actually do the hash

  ```js
  userSchema.pre(`save`, async function (next) {
    const user = this

    if (user.isModified(`password`)) {
      user.password = await bcrypt.hash(user.password, 8)
    }

    next()
  })
  ```

- #### Challenge: Change how tasks are updated

  - Find the task
  - Alter the task properties
  - Save the task
  - Test your work by updating

  ```js
  try {
    const task = await Task.findById(_id)

    if (!task) {
      return res.status(404).send()
    }

    updates.forEach((update) => (task[update] = req.body[update]))

    await task.save()

    res.status(200).send(task)
  } catch (e) {
    res.status(400).send(e)
  }
  ```

### 105 - Logging in Users

- We're hashing passwords. Great! Let's enable login
- We'll have them send us their username and password, and we'll have the API verify if that user exists
- We'll be using the user router and model
- We'll `POST` to a new `/users/login` route

  - We could either add all the code to the new route or create a reusable function to do all of that for us (we're going to do this one)
  - We'll add our function to the User schema. For now, we can set a bit of the request up

  ```js
  router.post(`/users/login`, async (req, res) => {
    try {
      const { email, password } = req.body
      const user = await User.findByCredentials(email, password)
    } catch (e) {}
  })
  ```

- Then, in the model, we'll add the new function to the schema. This is another thing we can only do because we created the schema separately.

  ```js
  userSchema.statics.findByCredentials = async (email, password) => {
    const user = await User.findOne({ email })

    if (!user) {
      throw new Error(`Unable to login`)
    }

    const isMatch = await bcrypt.compare(password, user.password)

    if (!isMatch) {
      throw new Error(`Unable to login`)
    }

    return user
  }
  ```

- We can now set up the rest of the login route

  ```js
  router.post(`/users/login`, async (req, res) => {
    try {
      const { email, password } = req.body
      const user = await User.findByCredentials(email, password)
      res.status(200).send(user)
    } catch (e) {
      res.status(400).send()
    }
  })
  ```

- But we also need to ensure there aren't any duplicate emails by adding `unique: true` to the email option in the user schema
- We should drop the DB and create a new user to test in Postman

### 106 - JSON Web Tokens

- We now have an HTTP request to login and validate
- Now let's let them be provably logged in
- We'll separate public and private routes
- We'll have login send back an authentication token
- We'll be using JWTs - JSON Web Tokens - for authentication
- We'll use the `jsonwebtoken` library, so install it with NPM
- We'll use the .sign() function that takes args and returns a token
  - It takes an object containing data embedded in the token, like a unique identifier for a user (\_id)
  - It also takes a string - a secret key to sign the token to ensure it hasn't been tampered with
- The JWT is made up of 3 distinct parts separated by `.`s
  - The header is a base64 encoded JSON string with meta information about the type of token and algorithm used to create it
  - The body is another base64 encoded string containing the payload - the data we put into the token
  - The signature helps verify the token
  - The body is not secure - it can be decoded using other information from the token - easily proven by taking the payload to base64decode.org
  - We can also prove it by using the verify() method on the token
- We can also add a third argument with options like `expiresIn` with options like `0 seconds`, `2 weeks`, `7 days`, etc.

  ```js
  import jsonwebtoken from 'jsonwebtoken'

  const demoFunction = async () => {
    const secret = `dactyl-hedgehog-columnar-bane-alleyway-finish-outclass-fructify`
    const token = jsonwebtoken.sign({ _id: `abc123` }, secret, {
      expiresIn: `7 days`,
    })
    console.log(token)

    const data = jsonwebtoken.verify(token, secret)
    console.log(data)
  }

  demoFunction()
  ```

### 107 - Generating Authentication Tokens

- Let's get back to the login endpoint and actually log folks in
- We'll also log people in who have just created accounts
- We should have an auth function we can use wherever we need to use it
- When we go to use it, it'll sit on the user instance instead of the model and look something like `const token = await user.generateAuthToken()`
- `/src/models/user.js`

  ```js
  userSchema.methods.generateAuthToken = async function () {
    const user = this
    const secret = `dactyl-hedgehog-columnar-bane-alleyway-finish-outclass-fructify`
    const token = jsonwebtoken.sign({ _id: user.id.toString() }, secret)

    return token
  }
  ```

- `/src/routers/user.js`
  ```js
  router.post(`/users/login`, async (req, res) => {
    try {
      const { email, password } = req.body
      const user = await User.findByCredentials(email, password)
      const token = await user.generateAuthToken()
      res.send({ user, token })
    } catch (e) {
      res.status(400).send()
    }
  })
  ```
- Test on Postman
- We should notice that the token isn't stored anywhere on the server, implying that users can't actually log out - if the token exists, it can be used
- Let's store multiple tokens per user so they can be logged in on multiple devices (laptop, phone, tablet, etc)
- We'll store tokens on the user document
  ```js
  const userSchema = new mongoose.Schema({
    ...
    // Add an array of token objects
    tokens: [
      {
        // Require a token string
        token: {
          type: String,
          required: true,
          // no validation or trimming necessary since we're generating this on the server
        },
      },
    ],
  })
  ```
- We can then add the generated token to the array as we generate it

  ```js
  userSchema.methods.generateAuthToken = async function () {
    ...
    // Add the token to the tokens array
    user.tokens = user.tokens.concat({ token })
    await user.save()

    return token
  }
  ```

- Looking at the token in the user document, we'll see that the subdocument gets an \_id as well
- #### Challenge: Have signup send back with token

  - Generate a token for the saved user
  - SEnd back both the token and the user
  - Create a new user from Postman and confirm the token is there

  ```js
  router.post(`/users`, async (req, res) => {
    const user = new User(req.body)
    try {
      await user.save()
      // generate token
      const token = await user.generateAuthToken()
      // send back in response
      res.status(201).send({ user, token })
    } catch (e) {
      res.status(400).send(e)
    }
  })
  ```

### 108 - Express Middleware

- Now that we have auth tokens, we should figure out how to use them to authenticate other requests
- For everything but signing up and logging in, we should require auth with Express Middleware
- The client will need the auth token
- We'll be working in `index.js`
- Express Middleware

  - Sits between the request and the route handler
  - We add a function like passing to route handler or preventing route handler
  - We can target individual routes
  - Uses `app.use()` and takes a function arg with req, res, and next as args

  ```js
  // This hangs in Postman...
  app.use((req, res, next) => {
    console.log(req.method, req.path)
  })
  // ...unless you call next()
  app.use((req, res, next) => {
    console.log(req.method, req.path)
    next()
  })
  ```

  - We may want to stop the route handler or send back an error

  ```js
  app.use((req, res, next) => {
    if (req.method === `GET`) {
      res.send(`GET requests are disabled`)
    } else {
      next()
    }
  })
  ```

- #### Challenge: Set up middleware for maintenance mode
  - Register a new middleware function
  - Send back a maintenance message with a 503 status code
  - Try your requests from the server and confirm status/message shows

### 109 - Accepting Authentication Tokens

### 110 - Advanced Postman

### 111 - Logging Out

### 112 - Hiding Private Data

### 113 - Authenticating User Endpoints

### 114 - The User/Task Relationship

### 115 - Authenticating Task Endpoints

### 116 - Cascade Delete Tasks
