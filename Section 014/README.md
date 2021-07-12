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
    req.send()
  })
  ...
  ```

- Images available at links.mead.io/files
### 124 - Validating File Uploads

### 125 - Validation Challenge

### 126 - Handling Express Errors

### 127 - Adding Images to User Profile

### 128 - Serving Up Files

### 129 - Auto-Cropping and Image Formatting

---
