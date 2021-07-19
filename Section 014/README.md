# The Complete Node.js Developer Course (3rd Edition)

## Section 14: File Uploads (Task App)

### 122 - Section Intro: File Uploads

- We know how to send JSONs, but what about files?
- We'll let people upload a profile picture
- We'll store a reference with other data and serve it with user profiles

### 123 - Adding Support for File Uploads

- How do we support file uploads with Express?
- Express doesn't support it by default, but they have an additional library that supports it: `npm i multer`, short for multi-parter
- We'll send it as form-data
- A basic example in `index.js`

  ```js
  ...
  // import the npm lib
  import multer from 'multer'

  // Create an instance of multer with a destination for the files
  const upload = multer({
    dest: `images`
  })

  // Create an upload POST handler with the instance's single()
  // function as middleware and a name for the upload as an argument.
  app.post(`/upload`, upload.single('upload'), (req, res) => {
    res.send()
  })
  ...
  ```

- Images available at links.mead.io/files
- Uploads go into image folder, but don't have extensions. We can manually add them to check the image, and will shortly learn how to add extensions.
- ### Challenge: Set up endpoint for avater upload

  - Add post /users/avatar to user router
  - Set up multer to store uploads in an avatars directory
  - Choose name "avatar" for the key when registering the middleware
  - Send back a 200 response from the route handler
  - Test your work. Create new Task App request and upload image.

  ```js
  ...
  import multer from 'multer'
  ...
  const avatar = multer({ dest: `avatars` })

  router.post(`/users/avatar`, avatar.single(`avatar`), (req, res) => {
    res.send()
  })
  ...
  ```

### 124 - Validating File Uploads

- Let's validate the stuff we're getting sent back
- We want to limit file uploads to 1-2MB and filetypes
- Everything can be configured on multer

  ```js
  const upload = multer({
    dest: `images`,
    limits: {
      fileSize: 1000000,
    },
  })
  ```

- We can also limit by file extension

  ```js
  const upload = multer({
    dest: `images`,
    limits: {
      fileSize: 1000000,
    },
    fileFilter(req, file, cb) {
      // File extension available on file obj
      // Let's validate for Word Docs
      // match checks for a regex match
      // This regex looks at the file ext with an escaped dot,
      // allowed extensions in a parentheses group, and a dollar
      // sign indicating it comes at the end of the string
      if (!file.originalname.match(/\.(doc|docx)$/)) {
        // If the ext isn't there, throw an error
        return cb(new Error(`Please upload a Word Document`))
      }
      // Otherwise, suggest everything went fine
      cb(undefined, true)
      // cb(new Error(`File must be a PDF`)) // Loudly reject
      // cb(undefined, true) // Everything went fine
      // cb(undefined, false) // silently reject
    },
  })
  ```

### 125 - Validation Challenge

- #### Challenge: Add validation to avatar uplaod route

  - Limit upload size to 1MB
  - Limit to JPG, JPEG, and PNG
  - Test your work

  ```js
  const avatar = multer({
    dest: `avatars`,
    limit: { fileSize: 1000000 },
    fileFilter(req, file, cb) {
      if (!file.originalname.match(/\.(jpg|jpeg|png)$/)) {
        return cb(new Error(`Please upload an image`))
      }
      cb(undefined, true)
    },
  })
  ```

### 126 - Handling Express Errors

- We'll send back a JSON error instead of the multer error
- Let's remove multer from /upload in index.js and add new middleware error

  ```js
  const errorMiddleware = (req, res, next) => {
    throw new Error(`Thrown by my middleware`)
  }

  app.post(`/upload`, errorMiddleware, (req, res) => {
    res.send()
  })
  ```

- The issue is that the error doesn't have a handler, so it uses the default Express handler
- We can add another function that takes an error, req, res, and next to act like error handling middleware

  ```js
  app.post(
    `/upload`,
    errorMiddleware,
    (req, res) => {
      res.send()
    },
    (error, req, res, next) => {
      res.status(400).send({ error: error.message })
    }
  )
  ```

- We can do this with our Multer middleware, too.

  ```js
  app.post(
    `/upload`,
    upload.single('upload'),
    (req, res) => {
      res.send()
    },
    (error, req, res, next) => {
      res.status(400).send({ error: error.message })
    }
  )
  ```

- #### Challenge: Clean up error handling

  - Set up an error handler function
  - send back a 400 with the error message
  - Test your work

  ```js
  router.post(
    `/users/avatar`,
    avatar.single(`avatar`),
    (req, res) => {
      res.send()
    },
    (error, req, res, next) => {
      res.status(400).send({ error: error.message })
    }
  )
  ```

### 127 - Adding Images to User Profile

- We need to create a link between an uploaded image and the user who uploaded it. It's not even authenticated
- We'll also add another route to delete profile photos
- We can add auth before the upload to add auth - easy as pie
- We need to move the files into the db rather than add files to the file system
- `models/user.js`

  ```js
  avatar: {
      type: Buffer,
    },
  ```

- Now how do we access the data from multer?
- If we remove the dest property from multer, it will pass the data through to the function we process the request with

  ```js
  router.post(
    `/users/avatar`,
    auth,
    avatar.single(`avatar`),
    // async function
    async (req, res) => {
      // Add file buffer/binary to user avatar field
      req.user.avatar = req.file.buffer
      // Save the user
      await req.user.save()
      // Send response
      res.send()
    },
    (error, req, res, next) => {
      res.status(400).send({ error: error.message })
    }
  )
  ```

- Test in Postman
- ### Challenge: Set up a route to delete avatar
  - Set up DELETE /users/me/avatar
  - Add authentication
  - Set the field to undefined and save the user sending back a 200
  - Test your work by creating a new request for Task App in Postman

### 128 - Serving Up Files

- How do we serve this up?
- We can serve it up with an image tag in a template in the browser
- We can also set up a URL to serve the image up like a normal file

  ```js
  // Get user avatar, not auth'd because it may be needed in feeds
  router.get(`/users/:id/avatar`, async (req, res) => {
    // Might not exist, so we should try/catch
    try {
      // Get user by id since it doesn't come from auth
      const user = await User.findById(req.params.id)
      // If the user doesn't exist or doesn't have an avatar, error
      if (!user || !user.avatar) {
        throw new Error()
      }
      // Set Content-Type header to image/jpg
      // Express usually handles for us, but not for this
      res.set(`Content-Type`, `image/jpg`)
      // Send the avatar data
      res.send(user.avatar)
    } catch (e) {
      // Catch errors
      res.status(404).send()
    }
  })
  ```

### 129 - Auto-Cropping and Image Formatting

- We can use npm package sharp to process images for size and filetype before we save them
- First, let's delete our image dirs since the avatars are in the DB and remove example code from `index.js` because we're not actually using it for anything
- Remove avatar data from user model so we can serve the user data faster if the avatar isn't necessary
- Let's `npm i sharp` and import it into the user router
  ```js
  router.post(
    `/users/avatar`,
    auth,
    avatar.single(`avatar`),
    async (req, res) => {
      req.user.avatar = req.file.buffer
      await req.user.save()
      res.send()
    },
    (error, req, res, next) => {
      res.status(400).send({ error: error.message })
    }
  )
  // BECOMES
  router.post(
    `/users/avatar`,
    auth,
    avatar.single(`avatar`),
    async (req, res) => {
      const buffer = await sharp(req.file.buffer)
        .resize({ width: 250, height: 250 })
        .png()
        .toBuffer()
      req.user.avatar = buffer
      await req.user.save()
      res.send()
    },
    (error, req, res, next) => {
      res.status(400).send({ error: error.message })
    }
  )
  ```

---
