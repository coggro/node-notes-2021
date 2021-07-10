# The Complete Node.js Developer Course (3rd Edition)

## Section 3: Node.js Module System (Notes App)

### 008 - Section Intro: Node.js Module System

- Starting to create app 1/4
- Introduces Node Modules system
- Connect to db, load in args
- Will learn to import core modules, other devs' modules, and our own modules

### 009 - Importing Node.js Core Modules

- All sorts of modules listed, and are available globally, like console
- File System requires import
- We'll be using fs.writeFile and fs.writeFileSync
- fs.writeFileSync(file, data to write)
- `/notes-app`

```js
fs.writeFileSync(`notes.txt`, `This file was created by Node.js!`)
```

- This won't run. Why not?
- fs is not defined.
- `/notes-app`

```js
const fs = require(`fs`)

fs.writeFileSync(`notes.txt`, `This file was created by Node.js!`)
```

- Can change the text and overwrite the file
- We could rename fs as variable, but not as a module
- #### **Personal Note**
  - You can do ES Modules if you have a `package.json` with `"type": "module"` in the JSON
- #### Challenge: Append a message to notes.txt

  - Use appendFileSync to append to the file
  - Run the script
  - Check your work by opening the file and viewing the appended text
  - `/app.js`

    ```js
    import fs from 'fs'

    // fs.writeFileSync(`notes.txt`, `My name is Corey`)

    fs.appendFileSync(`notes.txt`, `My name is Corey\n`)
    ```

### 010 - Importing Your Own Files

- Can load in from files we've created
- Right now, everything exists in app.js, and we want to split it up

  - create `/utils.js`

    ```js
    // Using Import
    const utilslog = () => {
      // Only this line is necessary using require()
      console.log(`utils.js`)
    }

    export default utilslog
    ```

  - Edit `./app.js`

    ```js
    import utilslog from './utils.js'
    // or require(`./utils.js`)

    utilslog()
    const name = `corey`

    console.log(name)
    ```

- Require seems to read the file in while import requires that it pass variables or functions that can be called or run
- They cannot coexist
- Removing name and recreating it in `utils.js` will cause it to fail since modules have their own scope
- Explicitly export with `module.export` or `export`

  - `./app.js`

    ```js
    import { name, utilslog } from './utils.js'

    console.log(name)
    ```

  - `./utils.js`

    ```js
    const utilslog = () => {
      console.log(`utils.js`)
    }

    const name = `corey`

    export { utilslog, name }
    ```

- Exporting a function works the same. It doesn't make much sense to import additional sandbox nonsense.
- #### Challenge: Define and use a function in a new file

  - Create a new file called notes.js
  - Create getNotes function that returns "Your notes..."
  - Export getNotes function
  - From app.js, load in and call the function printing message to console
  - `/notes.js`

    ```js
    const getNotes = () => {
      return 'Your notes...'
    }

    export { getNotes }
    ```

  - `/app.js`

    ```js
    import { getNotes } from './notes.js'

    console.log(getNotes())
    ```

### 011 - Importing npm Modules

- We can use core modules and custom modules, but what about npm?
- `npm init`
  - Initialize npm in root of project
  - Creates `./package.json`
- `npm i <package name>`
  - Install package in `node_modules` in project
  - I already did this to use `import`
  - We don't have to rebuild the wheel every time we need to do something. We can use others' work.
- We'll install `validator` with `npm i validator`
  - Creates `package-lock.json` and `node_modules` directory to store module files
    - `node_modules` has `validator` dir inside with `validator` files
    - `package-lock.json` stores the versions and a hash of modules we install for security
    - They're not supposed to be edited by hand.
  - He uses 10.8.0. We'll figure it out with the latest - 13.6.0
- Can then load it in with require/import and use it

  - `./app.js`

    ```js
    // const validator = require('validator')
    import validator from 'validator'

    import { getNotes } from './notes.js'

    console.log(getNotes())

    console.log(validator.isEmail(`coggro@gmail.com`))
    ```

- Check documentation for functions and stuff available in the package

### 012 - Printing in Color

- We're going to work with an npm module on our own
- We can reinstall `node_modules` based on `package-lock.json` and `package.json` with `npm i`.
- Core of video is a challenge to use an npm library - `chalk`, a package that gives color to the text and background behind text in the console
- #### Challenge: Use the chalk library in your project

  - Install chalk 2.4.1
  - Load chalk into app.js
  - Use it to print the string "Success!" to the console in green
  - Test your work
  - Bonus: Mess around with other styles - make text bold and inverse

  ```js
  import chalk from 'chalk'

  console.log(chalk.blue.inverse.bold('success'))
  ```

### 013 - Global npm Modules and nodemon

- Learning about global npm packages that we can run from the terminal
- `npm i -g <packageName>`
- We'll install `nodemon`, which restarts the app whenever we save changes and stops us from having to rerun the file every time to see changes
- Doesn't get used in a project or added to .jsons
- I've already done this. It's not that hard, and it is admittedly way useful

---
