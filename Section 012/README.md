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

  ```js
  app.use((req, res, next) => {
    res.status(503).send(`Service temporarily unavailable due to maintenance.`)
  })
  ```

### 109 - Accepting Authentication Tokens

- We're going to ass auth middleware using the same techniques
- We'll check for incoming auth, validate it, and find the associated user
- We did our initial middleware in the index, but it's better to do it in a separate file
- We'll create a `middleware` dir and an `auth.js` file inside
- `src/middleware/auth.js`

  ```js
  const auth = async (req, res, next) => {
    console.log(`auth middleare`)
    next()
  }

  export default auth
  ```

- When we define middleware in the index the way we did, it'll get used on every route - not neccessarily what we're looking for, especially with registering and logging in
- Let's remove our existing functions from index.js and head over to the user router to learn how to add middleware on a per-route basis

  ```js
  ...
  // import middleware into router
  import auth from '../middleware/auth.js'
  ...
  // Add it between route and handler function
  // Middleware will only run route if next() is called
  router.get(`/users`, auth, async (req, res) => {
    try {
      const users = await User.find({})
      res.status(200).send(users)
    } catch (e) {
      res.status(500).send()
    }
  })
  ```

- We can repeat for any routes requiring auth
- What will auth actually do? Auth starts with client taking login token and sending with request
- We'll add a header to the `GET /users` request for `Authorization: Bearer {token}`
- Let's add to the auth middleware

  ```js
  import jsonwebtoken from 'jsonwebtoken'
  import User from '../models/user.js'

  const auth = async (req, res, next) => {
    try {
      const token = req.header(`Authorization`).replace(`Bearer `, ``)
      const decoded = jsonwebtoken.verify(
        token,
        `dactyl-hedgehog-columnar-bane-alleyway-finish-outclass-fructify`
      )
      const user = await User.findOne({
        _id: decoded._id,
        'tokens.token': token,
      })

      if (!user) {
        throw new Error()
      }

      req.user = user
      next()
    } catch (e) {
      // If they don't validate, don't run next()
      res.status(401).send({ error: `Please authenticate.` })
    }
  }

  export default auth
  ```

- Major issue: We have information about other uses exposed on the `GET /users` route
- Let's change that route into a user profile endpoint

  ```js
  router.get(`/users/me`, auth, async (req, res) => {
    // Since the user is returned by auth and this route only
    // returns if auth hits next, we can just send back the user
    res.send(req.user)
  })
  ```

- Recap
  - Set up auth middleware for a route
  - Then wrote auth middleware to check for the token, decode and verify it, and confirm the user exists by finding them
  - If there's no user, we throw an error
  - If there is a user, we add it to the `req` and call `next()`
  - If any errors are thrown, we sned an error response

### 110 - Advanced Postman

- Let's cover some advanced Postman to make the large number of requests easier to handle
- Postman Environments and Postman Environment Variables
- Currently, all our requests are made to localhost; what about when we deploy?
- We'll create a new environment called `Task Manager API DEV` and add a `url` variable with initial value `localhost:3000` to it.
- We'll create a PROD environment as well with an empty `url` variable (until we know the production URL)
- We can use the DEV `url` by replacing `localhost:3000` with `{{url}}`
- We can update the rest of our routes
  - Yes, this is annoying
  - It does, however, make all of these much easier to use later on
- Currently, only one route has auth implemented, and it uses a static token header
- We could instead update them all to use the same auth scheme
- We can add the token to Auth in each request, but we could also add it to the entire collection
  - Login and Sign Up should have No Auth
- We can also automate the authorization by writing some JS
  - We can run code before the request in Pre-Request Script and after the script in Tests
  - Login User Tests
    ```js
    // If the response returns a 200 code...
    if (pm.response.code === 200) {
      // ...set the environmental variable `authToken` to the token
      // value from the JSONified response
      pm.environment.set(`authToken`, pm.response.json().token)
    }
    ```
  - We can now test this code and copy it over to creating the user (changing the response code to 201)
  - The auth token is now automated!
  - We can even see the value in the environment variable

### 111 - Logging Out

- Let's add an endpoint to log out and remove tokens
- We should add `req.token = token` to the auth middleware to get the validated and authorized token
- Then we can add another route to the user router and execute the logout

  ```js
  // POST /users/logout with auth middleware
  router.post(`/users/logout`, auth, async (req, res) => {
    try {
      // Replace users tokens with the same array filtered to remove
      // the authorized token (the only one that will return false)
      req.user.tokens = req.user.tokens.filter((token) => {
        return token.token !== req.token
      })
      // Save the user with the token removed
      await req.user.save()

      // Send a 200 OK code and nothing else
      res.send()
    } catch (e) {
      // If anything goes wrong, send a 500 Server Error
      res.status(500).send()
    }
  })
  ```

- #### Challenge: Create a way to logout of all sessions

  - Set up POST /users/logoutAll
  - create the router handler to wipe the tokens array
    - send 200 or 500
  - Test your work
    - Log in a few times and logout of all. Check DB

  ```js
  router.post(`/users/logoutall`, auth, async (req, res) => {
    try {
      req.user.tokens = []
      await req.user.save()

      res.send()
    } catch (e) {
      res.status(500).send()
    }
  })
  ```

### 112 - Hiding Private Data

- If we log in as a user, we see our password and tokens array with all of the user's tokens. We should lock all of this down.
- In the user router...
  ```js
  router.post(`/users/login`, async (req, res) => {
    try {
    ...
      // This fx doesn't exist, but it will soon
      res.send({ user: user.getPuglicPrifile(), token })
    }
    ...
  })
  ```
- In the user model...

  ```js
  userSchema.methods.getPublicProfile = function () {
    const user = this
    const userObject = user.toObject()

    return userObject
  }
  ```

- Logging in again, we see the same thing
- We should add...

  ```js
  delete userObject.password
  delete userObjec.tokens
  ```

- We then just get the relevant data, but it's fairly manual
- We can automate it so it works with all our handlers
- We can return to sending back just `{user}` if we rename the method to `toJSON`, it will replace the behavior of `JSON.stringify(...)` with the contents of the `toJSON` function.

### 113 - Authenticating User Endpoints

### 114 - The User/Task Relationship

### 115 - Authenticating Task Endpoints

### 116 - Cascade Delete Tasks
