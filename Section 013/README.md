# The Complete Node.js Developer Course (3rd Edition)

## Section 13: Sorting, Pagination, and Filtering (Task App)

### 117 - Section Intro: Sorting, Pagination, and Filtering

- Right now, whenever a client asks for data, they have little control of how that data is return
- Doing all of that processing will slow down our app
- We can provide search, sorting, and pagination to make our app way more efficient

### 118 - Working with Timestamps

- Let's enable timestamps on our models - Users and Tasks
- We'll enable an option that adds `createdAt` and `updatedAt` timestamps
- We customize our call to `mongoose.Schema()` to add a second object of options with `timestamps: true` inside
- Now, users will have timestamps
- Final DB wipe and create user
- We now see the timestamps on the user
- #### Challenge: Refactor Task model to add timestamps
  - Explicitly create schema
  - Set up timestamps
  - Create tasks from Postman to test work
  - This is simple and just requires imitating User

### 119 - Filtering Data
- Let's set up URL filter options with `GET /tasks`
- This is the only route that sends an array of data - potentially, hundreds of records
- It may even fetch things that the user isn't interested in using
- We'll do this with query strings - like how we set lat/lng and limits and such with previous APIs we've consumed
- We'll use `GET /tasks?completed={Boolean}` as the URL structure
- We can provide different arguments to populate to get this done
  
  ```js
  router.get(`/tasks`, auth, async (req, res) => {
    try {
      await req.user.populate(`tasks`).execPopulate()
      res.status(200).send(req.user.tasks)
    } catch (e) {
      res.status(500).send()
    }
  })
  // BECOMES
  router.get(`/tasks`, auth, async (req, res) => {
    // We'll put match here so we can define completed below
    const match = {}
    // If the query param completed exists...
    if (req.query.completed) {
      // It's equal to whether or not the string equals 'true'
      // Genius in its simplicity.
      match.completed = req.query.completed === 'true' 
    }
    try {
      // We refactor this to add in match
      await req.user.populate({
        path: `tasks`,
        match 
      }).execPopulate()
      res.status(200).send(req.user.tasks)
    } catch (e) {
      res.status(500).send()
    }
  })
  ```
### 120 - Paginating Data
- Let's add further options to refine data with pagination
- We should let them choose a page of data with 2 options - `limit` and `skip`
- `GET /tasks?limit={number}&skip={number}`

  ```js
  router.get(`/tasks`, auth, async (req, res) => {
    const match = {}
    if (req.query.completed) {
      match.completed = req.query.completed === 'true' 
    }
    try {
      await req.user.populate({
        path: `tasks`,
        match
      }).execPopulate()
      res.status(200).send(req.user.tasks)
    } catch (e) {
      res.status(500).send()
    }
  })
  // BECOMES
  router.get(`/tasks`, auth, async (req, res) => {
    const match = {}
    if (req.query.completed) {
      match.completed = req.query.completed === 'true' 
    }
    try {
      await req.user.populate({
        path: `tasks`,
        match,
        // Options lets us pass in limit and skip from req.query
        options: {
          limit: parseInt(req.query.limit),
          skip: parseInt(req.query.skip)
        }
      }).execPopulate()
      res.status(200).send(req.user.tasks)
    } catch (e) {
      res.status(500).send()
    }
  })
  ```
### 121 - Sorting Data
- And finally, sort
- We'll probably need `sortBy` (field) and `order` (asc/des)
- `GET /tasks?sortBy=createdAt_asc` should do it
  ```js
  router.get(`/tasks`, auth, async (req, res) => {
    const match = {}
    if (req.query.completed) {
      match.completed = req.query.completed === 'true' 
    }
    try {
      await req.user.populate({
        path: `tasks`,
        match,
        // Options lets us pass in limit and skip from req.query
        options: {
          limit: parseInt(req.query.limit),
          skip: parseInt(req.query.skip)
        }
      }).execPopulate()
      res.status(200).send(req.user.tasks)
    } catch (e) {
      res.status(500).send()
    }
  })
  // BECOMES
  router.get(`/tasks`, auth, async (req, res) => {
    const match = {}
    // Set up empty sort object
    const sort = {}
    if (req.query.completed) {
      match.completed = req.query.completed === 'true' 
    }
    // If there's a sortBy...
    if (req.query.sortBy) {
      // ...split it up by the underscore...
      const parts = req.query.sortBy.split(`_`)
      // ...and assign the attribute accordingly
      sort[parts[0]] = parts[1] === 'desc' ? -1 : 1
    }
    try {
      await req.user.populate({
        path: `tasks`,
        match,
        options: {
          limit: parseInt(req.query.limit),
          skip: parseInt(req.query.skip),
          sort // then toss it in the object
        }
      }).execPopulate()
      res.status(200).send(req.user.tasks)
    } catch (e) {
      res.status(500).send()
    }
  })
  ```