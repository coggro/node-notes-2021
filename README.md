# The Complete Node.js Developer Course (3rd Edition)

## Section 1: Welcome

### 001 - Welcome to the Class!

- Started teaching over 5 years ago, lots of new features
- Will learn everything to be a pro
- Course Curriculum
  - 4 major parts with a project
    - Note-Taking
      - Basic
      - Create an app
      - Use basic features
      - Run apps
      - File system IO
      - 3rd party library
    - Weather
      - Web application
      - Create web server
      - Node APIs
      - Production server
      - 3rd party services
    - Task Manager
      - Authentication
      - DB storage
      - File uploads
      - Email sending
    - Real-Time Chat
      - Socket.io for real-time apps
  - Learn to use features together
- How to get the most out of the class
  - Interact with the code. Do the same things in the video.
  - Do the 100+ unique challenges. Try before looking at the solution.
- How to get help
  - Use the Q&A

### 002 - Grab the PDF Guide

- A PDF reference eBook has info for every lesson

---

## Section 2: Installing and Exploring Node.js

### 003 - Section Intro: Installing and Exploring Node.js

- Getting our machine set up with Node and VSCode
- Talking about what Node is

### 004 - Installing Node.js and VSCode

- I know more than this. NVM and my existing VSCode install are fine.
- Just throw the latest version in.
- node -v --> Node version installed

### 005 - What is Node.js?

- JS devs took JS and made it a standalone process
- Previously was limited to the browser
- Node took JS out to make servers and backends and such
- How is this possible?
- Node.js is a JavaScript runtime built on Chrome's V8 JavaScript engine.
- V8 is in C++ and is extended in C++ as well.
- The runtime provides it with specific tools and such - browser gives buttons and such, Node gets file system and server utils
- Code Example
  - Browser console with a few functions
    - window, document objects are restricted to browser
  - Terminal REPL (Read Evaulate Print Loop) with a few functions
    - global, process objects are restricted to Node

### 006 - Why Should I Use Node.js?

- Increasingly chosen for major tasks
- All along the stack if you so choose it
- Also useful on command-line
- Node.js uses an event-driven non-blocking I/O model that makes it lightweight and efficient. Node.js' package ecosystem, npm, is the largest ecosystem of open source libaries in the world.
  - Non-blocking I/O - can continue to process code and such while waiting for requests
    - Actually originated in the browser and got brought out
    - Critical Feature of Node
- NPM is an enormous database of open source packages

### 007 - Your First Node.js Script

- Going to create and run our first script
- Suggests using integrated terminal in VS Code. Meh.
- Create `node-course` folder
- `/node-course/hello.js`
  ```js
  console.log(`Welcome to the class!`)
  ```
- Run with `node hello.js`

---

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

## Section 4: File System and Command Line Args (Notes App)

### 014 - Section Intro: File System and Command Line Args

- Continue learning to work with Node modules
- Create Notes App
- Will be using fs and yargs for file management and CLI args

### 015 - Getting Input from Users

- How do we get input from the user?
- We'll learn with the CLI for now, but will later use browser forms
- We can add more arguments after script name and it runs and passes that info in... but how do we access them?
- We use `process` global variable. `process.argv` to be exact.
  - The first argument is the path to the Node binary that runs the script
  - The second argument is the path to the script
  - Arguments 3 and on are data passed in
- We can use additional information to pass in commands (Add Note) and pass in data (text of note) etc.
- We can add command line options as well. If done with argv, we'd need to parse them. We can instead use `yargs`, a package purpose-built for this.

### 016 - Argument Parsing with Yargs: Part I

- We can use `yargs` to get arguments in a more useful way
- We're using a lot of packages... what about actual Node? Well, this is how Node junk gets developed.
- `npm i yargs`
- `./apps.js`

  ```js
  import chalk from 'chalk'
  import yargs from 'yargs'
  import { hideBin } from 'yargs/helpers'
  import { getNotes } from './notes.js'

  console.log(process.argv)
  console.log(yargs(hideBin(process.argv)).argv)
  ```

- Yargs requires the hideBin to work with import
- This exposes a much more reasonable object of arguments
  - `node app.js add --title="things to buy"` yields `{ _: [ 'add' ], title: 'things to buy', '$0': 'app.js' }`
- `yargs` automatically generates `--help` text
- We can set up commands using `.command({command object})` function

  ```js
  import chalk from 'chalk'
  import yargs from 'yargs'
  import { hideBin } from 'yargs/helpers'
  import { getNotes } from './notes.js'

  // add, remove, read, list

  // Create Add Command
  // Setting the yargs with hideBin() to a const seems to help
  const args = yargs(hideBin(process.argv))

  args
    .command({
      command: `add`,
      description: `Add a new note`,
      handler: () => {
        console.log(`Adding a new note.`)
      },
    })
    .command({
      command: `remove`,
      description: `Remove a note.`,
      handler: () => {
        console.log(`Removing a note.`)
      },
    })

  console.log(args.argv)
  ```

- Challenge is to create read and list commands, which is trivial repetition of creating other commands.

### 017 - Argument Parsing with Yargs: Part 2

- We'll add support for options for commands
- We'll use builder object on the commands

  ```js
  const args = yargs(hideBin(process.argv)).command({
    command: `add`,
    description: `Add a new note`,
    builder: {
      title: {
        describe: `Note title`, // Describe option for help
        demandOption: true, // Require option
        type: `string`, // Force a string value
      },
    },
    // Add argv to arguments here and use in fx
    handler: (argv) => {
      console.log(`Title: ${argv.title}`)
    },
  })
  ...
  // Accessing argv on the actual args object forces it to actually parse. If we don't do it here, it won't parse.
  // console.log(args.argv)

  // Force a parse without logging
  args.parse()
  ```

- #### Challenge: Add an option to yargs
  - Set up a body option for the add command
  - Configure a description, make it required, and force a string type
  - Log the body value in the handler function
  - Test your work!
  ```js
  .command({
    command: `add`,
    description: `Add a new note`,
    builder: {
      title: {
        describe: `Note title`, // Describe option for help
        demandOption: true, // Require option
        type: `string`, // Force a string value
      },
      // Solution
      body: {
        describe: `Note body`,
        demandOption: true,
        type: `string`,
      },
    },
    // Add argv to arguments here and use in fx
    handler: (argv) => {
      let { title, body } = argv // Also decided to destructure here
      console.log(`Title: ${title}\nBody: ${body}`)
    },
  })
  ```

### 018 - Storing Data with JSON

- We know how data goes into the program, but what do we do with it then? How do we save it to make sure it's usable afterwards?
- We'll use a JSON file with `fs`
- JSON is a popular, extensively used format
- Let's explore JSON in a `./playground` directory
- `fs` only knows how to work with strings, so we need to make our JSON a string.

  ```js
  import fs from 'fs'
  // Create a book object
  const book = {
    title: `Ego Is The Enemy`,
    author: `Ryan Holiday`,
  }

  // Turn it into a JSON string
  const bookJSON = JSON.stringify(book)
  console.log(bookJSON)
  // {"title":"Ego Is The Enemy","author":"Ryan Holiday"}

  // Parse string back into object
  const parsedData = JSON.parse(bookJSON)
  console.log(parsedData.author)
  // Ryan Holiday

  // Stringify the book content
  const bookJSON = JSON.stringify(book)
  // Write it to the file
  fs.writeFileSync(`1-json.json`, bookJSON)
  ```

- Reading from the file

  Need to do this separately from writing so it doesn't overwrite every time or something... The logic of the program would dictate, for this one we'll just follow along.

  ```js
  // get binary data
  const dataBuffer = fs.readFileSync(`1-json.json`)
  // convert it to a string
  const dataJSON = dataBuffer.toString()
  // and parse it
  const data = JSON.parse(dataJSON)
  console.log(data.title)
  ```

- We now know how to do everything we need... but let's do a challenge!
- #### Challenge: Work with JSON and the file system
  - Load and parse the JSON data
    - {"name":"Andrew","planet":"Earth","age":27}
  - Change the name and agee property using your info
  - STringify the changed object and overwrite the original data
  - Test your work by viewing data in the JSON file
  ```js
  const data = JSON.parse(fs.readFileSync(`1-json.json`).toString())
  console.log(data)
  data.name = `Corey`
  data.age = 31
  fs.writeFileSync(`1-json.json`, JSON.stringify(data))
  const modifiedData = JSON.parse(fs.readFileSync(`1-json.json`).toString())
  console.log(modifiedData)
  ```

### 019 - Adding a Note

- Let's wire up adding a note
- We'll work in `/notes-app` with `app.js` and `notes.js` and can delete `notes.txt` since we'll be using JSON
- `./notes.js`

  ```js
  // Get fs imported
  import fs from 'fs'

  const getNotes = () => {
    return 'Your notes...'
  }
  // AddNote fx
  const addNote = function (title, body) {
    // Use loading fx to make writing other fxs easier
    const notes = loadNotes()
    // Filter through to find duplicate notes
    const duplicateNotes = notes.filter((note) => {
      return note.title === title
    })
    // If there are no cuplicates, add the note
    if (duplicateNotes.length === 0) {
      notes.push({ title, body })
      console.log(`Note added.`)
    } else {
      // Otherwise, just say it's taken
      console.log(`Note title taken.`)
    }
    // Use saving fx to make writing other fxs easier
    saveNotes(notes)
  }
  // Loading fx
  const loadNotes = function () {
    // Try loading from expected file
    try {
      return JSON.parse(fs.readFileSync(`notes.json`).toString())
    } catch (e) {
      // If there are any errors, just give an empty array, the same result as
      // though the file existed but was empty
      return []
    }
  }
  // Saving fx, writes JSON string to file
  const saveNotes = (notes) => {
    fs.writeFileSync(`notes.json`, JSON.stringify(notes))
  }
  export { getNotes, addNote }
  ```

### 020 - Removing a Note

- Remove note in 3 challenges
- #### Challenge: Setup command option and function

  - Setup the remove command to take a required --title option
  - Create and export a removeNote function from notes.js
  - Call removeNote in remove command handler
  - Have removeNote log the title of the note to be removed
  - Test your work

  ```js
  //app.js
  ...
  import { getNotes, addNote, removeNote } from './notes.js'
  ...
  .command({
    command: `remove`,
    description: `Remove a note.`,
    builder: {
      title: {
        describe: `Note title`,
        demandOption: true,
        type: `string`,
      },
    },
    handler: (argv) => {
      let { title } = argv
      removeNote(title)
    },
  })
  ...
  //note.js
  const removeNote = (title) => {
    console.log(`Note "${title}" will be removed.`)
  }
  ...
  export { getNotes, addNote, removeNote }
  ```

- #### Challenge: Wire up removeNote

  - Load existing notes
  - Use array filter method to remove the matching note (if any)
  - Save the newly created array
  - Test your work with a title that exists and a title that doesn't exist

  ```js
  const removeNote = (title) => {
    const notes = loadNotes()

    const remainingNotes = notes.filter((note) => {
      return note.title !== title
    })
    if (remainingNotes.length < notes.length) {
      console.log(`Note "${title}" will be removed.`)
    } else {
      console.log(`No note exists with that title. No notes have been removed.`)
    }

    saveNotes(remainingNotes)
  }
  ```

- #### Challenge: Use chalk to provide useful logs to remove
- If a note is removed, print "Note removed!" with a green background
- If no note is removed, print "No note found!" with a red background

  ```js
  ...
  import chalk from 'chalk'
  ...
  // Moved above the prints
  saveNotes(remainingNotes)

  // This adds chalk
  if (remainingNotes.length < notes.length) {
    console.log(chalk.green.inverse(`Note removed.`))
  } else {
    console.log(
      chalk.red.inverse(
        `No note exists with that title. No notes have been removed.`
      )
    )
  }
  ```

### 021 - ES6 Aside: Arrow Functions

- Let's talk arrow functions
- `playground/2-arrow-functions.js`

  ```js
  // OG function declaration
  // const square = function (x) {
  //   return x * x
  // }

  // Arrow fx
  // const square = (x) => {
  //   return x * x
  // }

  // Arrow fx w/ shorthand
  // const square = (x) => x * x

  // console.log(square(3))

  const event = {
    name: `Birthday Party`,
    guestList: [`Andrew`, `Jen`, `Mike`],
    // This alternative syntax binds this, but is shorter
    printGuestList() {
      // this isn't bound in an arrow function, so function fails
      console.log(`Guest List for ${this.name}`)
      // We don't want to bind this here, so arrow functions are
      // helpful since we don't want the printGuestList this value
      this.guestList.forEach((guest) =>
        console.log(`${guest} is attending ${this.name}`)
      )
    },
  }

  event.printGuestList()
  ```

### 022 - Refactoring to Use Arrow Functions

- `playground/3-arrow-challenge.js`

  ```js
  //
  // Goal: Create method to get incomplete tasks
  //
  // 1. Define getTasksToDo method
  // 2. Use filter to to return just the incompleted tasks (arrow function)
  // 3. Test your work by running the script

  const tasks = {
    tasks: [
      {
        text: 'Grocery shopping',
        completed: true,
      },
      {
        text: 'Clean yard',
        completed: false,
      },
      {
        text: 'Film course',
        completed: false,
      },
    ],
    getTasksToDo() {
      return this.tasks.filter((task) => !task.completed)
    },
  }

  console.log(tasks.getTasksToDo())
  ```

- #### Challenge
  - https://gist.github.com/andrewjmead/ad7a587411aa8e3fb3ea643c37c45818
- I've been using arrow functions the whole time.

### 023 - Listing Notes

- This is the easiest one out of all of them.
- #### Challenge: Wire up list command

  - Create and export listNotes from notes.js
    - Your Notes using chalk
    - Print note title for each note
  - Call listNotes from command handler
  - Test your work
  - `notes.js`

  ```js
  ...
  const listNotes = () => {
    const notes = loadNotes()

    console.log(chalk.green.inverse(`Your Notes`))
    notes.forEach((note) => {
      console.log(note.title)
    })
  }
  ...
  export { getNotes, addNote, removeNote, listNotes }
  ```

  - `app.js`

  ```js
  ...
  import { addNote, removeNote, listNotes } from './notes.js'
  ...
  .command({
    command: `list`,
    description: `List out all notes.`,
    handler: () => {
      listNotes()
    },
  })
  ...
  ```

### 024 - Reading a Note

- Finishing up the app by wiring up the read command
- We use filter in addNote and removeNote to eliminate duplicates and remove notes. We can improve addNote.

  - Filter will look through every note, not stopping when it finds a duplicate.
  - `app.js`

  ```js
  const addNote = (title, body) => {
    const notes = loadNotes()

    const duplicateNote = notes.find((note) => note.title === title)

    if (!duplicateNote) {
      notes.push({ title, body })
      console.log(`Note added.`)
    } else {
      console.log(`Note title taken.`)
    }
    saveNotes(notes)
  }
  ```

- #### Challenge: Wire up read command

  - Setup --title option for read command
    - Search for note by title
    - Find note and print title (styled) and body (plain)
    - No note found? Print error in red
  - Have the command handler call the function
  - Test your work by running a couple commands
  - `notes.js`

  ```js
  ...
  const readNote = (title) => {
    const notes = loadNotes()
    const targetNote = notes.find((note) => note.title === title)

    if (targetNote) {
      console.log(chalk.green.inverse(targetNote.title))
      console.log(targetNote.body)
    } else {
      console.log(chalk.red.inverse(`Note not found.`))
    }
  }
  ...
  export { getNotes, addNote, removeNote, listNotes, readNote }
  ```

  - `app.js`

  ```js
  ...
  import { addNote, removeNote, listNotes, readNote } from './notes.js'
  ...
  .command({
    command: `read`,
    description: `Reading a note.`,
    builder: {
      title: {
        describe: `Note title`,
        demandOption: true,
        type: `string`,
      },
    },
    handler: (argv) => {
      let { title } = argv
      readNote(title)
    },
  })
  ...
  ```

- Can remove getNotes

---

## Section 5: Debugging Node.js (Notes App)

### 025 - Section Intro: Debugging Node.js

- Going to learn how to debug Node programs
- Lots of typos :rofl:
- Perfection is not realistic

### 026 - Debugging Node.js

- How to best debug Node applications
- Will explore Node debugger and Chrome dev tools
- Generally 2 types of errors:
  - Something goes wrong and there's an explicit error
  - Something goes wrong and there's not a message, it's just wrong data
- Basic debugging tool is the console
- Can also use Node Debugger, which integrates with dev tools
  - Use `debugger` in code to add a breakpoint that only stops on `node inspect <scriptname> <args>`
  - Go to Chrome and hit `chrome://inspect`
  - Remote Target should list your script
  - `inspect` wraps the code in a function
  - Debugger lets you step through the code line by line or debugger pause to pause.

### 027 - Error Messages

- Look at error messages and learn to find errors.
- I've been at this a while. I know how to do this.

---

## Section 6: Asynchronous Node.js (Weather App)

### 028 - Section Intro: Asynchronous Node.js

- Node is async, non-blocking, event-driven
- What do these mean?
- We'll move on to next app and use location services and a weather API
- We'll use Async JS all over the place

### 029 - Asynchronous Basics

- Going to create our first async/non-blocking application
- Starting new project in `/weather-app` directory with `app.js` file
- `app.js`

  ```js
  console.log(`Starting`)

  setTimeout(() => {
    console.log(`Time Out 1`)
  }, 2000)
  // With just this, output is Starting/Stopping/Time Out 1
  // setTimeOut is async, so it runs the rest of the sync code before
  // going back to do callbacks

  setTimeout(() => {
    console.log(`Time Out 2`)
  }, 0)
  // With this added, output is Starting/Stopping/Time Out 2/Time Out 1
  // They're still async, so the second one waits for callback also and then
  // instantly fires - but not before the rest of the sync code

  console.log(`Stopping`)
  ```

### 030 - Call Stack, Callback Queue, and Event Loop

- We had a lot of questions about how async actually works
- Watch this one again after building Weather and have more experience
- Call Stack, Node APIs, Callback Queue, and Call Stack work together to run Node
  - Call Stack
    - Simple data structure that tracks execution of the program by tracking programs currently running
    - Stack trace shows call stack
    - You can add an item on top and remove the top item, and that's it
  - Node APIS
    - Registers events from Node fxs and has a callback paired with it
  - Callback Queue
    - A line of callback fxs that are waiting to be called when the call stack is empty
  - Event Loop
    - Looks at the call stack and callback queue. If the stack is empty, it runs the Callback Queue
- Basic Synchronous Code
  - Running an app runs a main function to wrap normal code
  - Statements run normally
  - Functions get added to the call stack on top of main, and then get removed from the call stack
  - Main finishes, call stack empties, program is done
- Synchronous With More Functions
  - Main function
  - listLocations gets defined, but isn't called
  - Var defined
  - Function call goes on call stack, gets executed
  - Function in function gets called, gets executed
  - Inner fx gets taken off, followed by outer fxs in layers
  - Loops put fx on and take them off as many times as necessary
  - Once all fxs are done, main comes off stack, program ends.
- Async Example
  - This is the code we wrote before with timeouts
  - Main gets called
  - Log goes on, executes, and gets removed
  - setTimeOut goes on, but is actually a Node API
    - Wait 2 seconds,
  - setTimeOut goes to Node API, allowing execution to continue
    - JS is single-threaded, but Node uses multiple threads with C++ behind the scenes, so the rest of the app can run.
  - setTimeOut runs again, goes to Node API
  - 0 seconds STO goes to the callback queue
  - main is still on the stack, so it continues to run
  - Second log goes on, executes, and comes off
  - main comes off. Normally, program finishes.
  - Event loop puts 0 second STO on stack to execute and remove
  - 2sSTO will then finish and go on the queue and stack, execute, and be removed

### 031 - Making HTTP Requests

- Let's make HTTP requests from Node apps!
- This is how we talk to the outside world
  - Emails, texts, etc. all do http
  - Send a request to an API that will do what we need
  - May return data to use
- We'll use weatherstack, a real-time weather API with some small changes from darksky as it was originally coded
- We can make a request to the url and get data back, but they quartered the free tier, so that sucks.
- api.weatherstack.com/current?access_key=API_KEY&query=37.8267,-122.4233
  - Gives the API our access key and lat/lng for a request
- Firefox parses JSON, but we can use Raw Data for the raw data
- We can use this URL to make requests in our app.
- He suggests using the `request` module, which is deprecated. I'd rather mess with axios...
- `npm init -y // -y blows through default options`
- `npm i request`
- Can make a request to the URL and get the data back as a variable
- `request(options, callback)`
  - `options` is an object of options, `url` being mandatory
  - `callback` runs with `error, response` arguments
    - console.logging response vomits hundreds of lines of code to the terminal
    - Really long string in it is JSON data returned
    - Can parse data to JSON like normal data and console.log it again to get an object
    - Lots of properties show up there, and he uses `data.current` for current weather data

### 032 - Customizing HTTP Requests

- Get request to automatically parse data, print a forecast, and explore options for API to change language and units
- We can use the `"json": true` option to get request to parse JSON for us
  - app.js
    ```js
    request({ url: url, json: true }, (error, response) => {
      console.log(response.body.current) // This is now an object, negating the
      // use for the parsing step
    })
    ```
- #### Challenge: Print a small forecast to the user
  - Print: "It is currently XX degrees out. It feels like XX degrees out."
  - Weatherstack is slightly different than darksky, so the challenge changed a bit. No big deal. It's a console log of object properties.
- Can request different units using a `&units=f` units parameter.
- Forecast is a different URL
  - He suggests using the browser to find the information you want off of the JSON, using an extension like JSON Formatter
- Other options are detailed in the API

### 033 - An HTTP Request Challenge

- #### Challenge: Geocoding
  - Fire off a new request to the URL explored in browser
  - Have the request module parse it as JSON
  - Print bot the latitude and longitude to the terminal
  - Test your work
  - `app.js`
  ```js
  request({ url: geoURL, json: true }, (error, response) => {
    console.log(response.body)
    const LAT = response.body.features[0].center[1]
    const LON = response.body.features[0].center[0]
    console.log(`Lat: ${LAT}, Lon: ${LON}`)
  })
  ```

### 034 - Handling Errors

- How do we handle errors on requests?
- We should check for an error before handling the response
- We can check for the error in the error argument when no network exists
  - `if (error) { print error message } else { handle result }`
- Can also handle bad user input
  - Bad data sends back error code, but this counts as a response
  - Also need to look for an error code in the response
  - add `else if (response.body.error) { handle error }`
- #### Challenge: Geocoding error handling
  - Setup an error handler for low-level errors
  - Test by disabling network request and running the app
  - Setup error handling for no matching results
  - Test by altering the search term and running the app
  ```js
  // *** Geo Request ***
  request({ url: geoURL, json: true }, (error, response) => {
    if (error) {
      console.log(error)
    } else if (response.body.features.length === 0) {
      console.log(`No results returned.`)
    } else {
      const LAT = response.body.features[0].center[1]
      const LON = response.body.features[0].center[0]
      console.log(`Lat: ${LAT}, Lon: ${LON}`)
    }
  })
  ```

### 035 - The Callback Function

- Callbacks are the root of async JS
- Let's work in `/playground/4-callbacks.js`

  ```js
  // First argument is a callback fx, a function that is passed as an
  // argument to be called later on. setTimeOut is async, but the
  // callback pattern is not necessarily async.
  setTimeout(() => {
    console.log(`Two seconds are up`)
  }, 2000)

  // This is a synchronous callback pattern
  const names = [`andrew`, `jen`, `jess`]
  const shortNames = names.filter((name) => {
    return name.length <= 4
  })
  ```

- Now how about `app.js`?
  - What if we had 4 places to geocode?
  - Would need to copy code and put in several places
  - Could create a geocode fx with code inside
- Return to `/playground/4-callbacks.js`

  ```js
  const geocode = (address, callback) => {
    setTimeout(() => {
      // mock data
      // when inside setTimeout, it messes with console.log sync code
      const data = {
        latitude: 0,
        longitude: 0,
      }
      // return data

      // can instead use the callback on the data
      callback(data)
    }, 2000)

    // could return here, or could do callback
    // sans callback, not async
    // return data
  }

  // Sans callback, not async
  // Not actually useful when we use async callback
  // const data = geocode(`Philadelphia`)
  // console.log(data)

  // can then use the callback to process the data
  geocode(`Philadelphia`, (data) => {
    console.log(data)
  })
  ```

- #### Challenge:

  - links.mead.io/callback
  - Take code and paste in, comment everything else out

  ```js
  //
  // Goal: Mess around with the callback pattern
  //
  // 1. Define an add function that accepts the correct arguments
  // 2. Use setTimeout to simulate a 2 second delay
  // 3. After 2 seconds are up, call the callback function with the sum
  // 4. Test your work!

  const add = (num, ber, callback) => {
    setTimeout(() => {
      callback(num + ber)
    }, 2000)
  }

  add(1, 4, (sum) => {
    console.log(sum) // Should print: 5
  })
  ```

### 036 - Callback Abstraction

- We're going to create a real HTTP request in the fx
- We can make the code maintainable and sequential
- We should have two discrete fxs for each thing we're trying to do
- We'll convert geocode and weather (challenge) to functions
- `/weather-app/app.js`

  ```js
  ...
  import geocode from './utils/geocode.js'
  ...
  geocode(`120 Holly Dr, Hatboro, PA, 19040`, (err, data) => {
    console.log(`Error`, err)
    console.log(`Data`, data)
  })
  ```

- `/weather-app/utils/geocode.js`

  ```js
  import request from `request`

  import { mapboxKey } from '../keys.js`

  const geocode = (address, callback) => {
    const geoURL = `https://api.mapbox.com/geocoding/v5/mapbox.places/${encodeURIComponent(
      address
    )}.json?access_token=${mapboxKey}&limit=1&language=en`

    request({ url: geoURL, json: true }, (err, res) => {
      if (err) {
        callback(`Unable to connect to location services.`, undefined)
      } else if (res.body.features.length === 0) {
        callback(`Unable to find location. Try another search.`)
      } else {
        callback(undefined, { // these are wrong, swap the lat and long indices
          latitude: res.body.features[0].center[0],
          longitude: res.body.features[0].center[1],
          location: res.body.features[0].place_name,
        })
      }
    })
  }

  export default geocode
  ```

### 037 - Callback Abstraction Challenge

- We'll do what we did for geocode for the weather call. We can remove previous code.
- links.mead.io/forecast

  ```js
  //
  // Goal: Create a reusable function for getting the forecast
  //
  // 1. Setup the "forecast" function in utils/forecast.js
  // 2. Require the function in app.js and call it as shown below
  // 3. The forecast function should have three potential calls to callback:
  //    - Low level error, pass string for error
  //    - Coordinate error, pass string for error
  //    - Success, pass forecast string for data (same format as from before)

  forecast(-75.7088, 44.1545, (error, data) => {
    console.log('Error', error)
    console.log('Data', data)
  })
  ```

- `/weather-app/utils/forecast.js`

  ```js
  import request from 'request'

  import { weatherstackKey } from '../keys.js'

  const forecast = (latitude, longitude, callback) => {
    const forecastURL = `http://api.weatherstack.com/current?access_key=${weatherstackKey}&query=${longitude},${latitude}&units=f`

    request({ url: forecastURL, json: true }, (err, res) => {
      if (err) {
        callback(`Unable to connect to forecast services.`, undefined)
      } else if (res.body.error) {
        callback(res.body.error.info, undefined)
      } else {
        callback(
          undefined,
          `${res.body.current.weather_descriptions}. It is currently ${res.body.current.temperature} degrees out. It feels like ${res.body.current.feelslike} degrees out.`
        )
      }
    })
  }

  export default forecast
  ```

- `/weather-app/app.js`

  ```js
  import forecast from './utils/forecast.js'
  import geocode from './utils/geocode.js'

  geocode(`120 Holly Dr, Hatboro, PA, 19040`, (err, data) => {
    console.log(`Error`, err)
    console.log(`Data`, data)
  })

  forecast(-75.7088, 44.1545, (error, data) => {
    console.log('Error', error)
    console.log('Data', data)
  })
  ```

- I got it almost spot-on. He just used a different error message in the middle. That's pretty cool, but also expected since I have 5 years experience in web dev...

### 038 - Callback Chaining

- Let's learn how to chain callbacks
- We have two async functions operating independent of one another
- If we chain them, we can do one and then the next.
- `/weather-app/app.js`

  ```js
  import forecast from './utils/forecast.js'
  import geocode from './utils/geocode.js'
  // refactor data vars
  geocode(`120 Holly Dr, Hatboro, PA, 19040`, (err, geoData) => {
    if (err) {
      // return to leave fx and stop executing code
      return console.log(error)
    }
    forecast(geoData.latitude, geoData.longitude, (err, forecastData) => {
      if (err) {
        return console.log(err)
      }
      console.log(geoData.location)
      console.log(forecastData)
    })
  })
  ```

- #### Challenge: Accept location via CLI arg

  - Access the CLI arg without args
  - Use the string value as the input for geocode
  - Only geocode if a location was provided
  - Test your work with a couple locations

- `/weather-app/app.js`

  ```js
  import forecast from './utils/forecast.js'
  import geocode from './utils/geocode.js'
  // get location from arguments
  const location = process.argv[2]
  // if it exists, do the things
  if (location) {
    geocode(location, (err, geoData) => {
      if (err) {
        return console.log(error)
      }
      forecast(geoData.latitude, geoData.longitude, (err, forecastData) => {
        if (err) {
          return console.log(err)
        }
        console.log(geoData.location)
        console.log(forecastData)
      })
    })
  } else {
    console.log(`Please provide a location in the CLI args.`)
  }
  ```

### 039 - ES6 Aside: Object Property Shorthand and Destructuring

- This video is about ES6 features with objects
- create `/playground/5-es6-objects.js`

  ```js
  // Object property shorthand
  const name = `Corey`
  const userAge = 31

  // properties defined by variables of the same name can just get passed in
  const user = {
    name,
    userAge, // have to use userAge, not age - name must match exactly
    location: `Philadelphia`,
  }

  console.log(user)

  // Object destructuring
  const product = {
    label: `Red Notebook`,
    price: 3,
    stock: 201,
    salePrice: undefined,
  }

  // Destructure properties off of the object...
  // You can make new names using the : name syntax used for label here
  // Can set default values as with rating
  const { label: productLabel, price, stock, salePrice, rating = 5 } = product
  // ...for use here
  console.log(label, price, stock, salePrice)

  // Can destructure it inline as well, like in fx definitions
  const transaction = (type, { label, stock }) => {
    console.log(type, label, stock)
  }

  transaction(`order`, product)
  ```

### 040 - Destructuring and Property Shorthand Challenge

#### Challenge: Use both destructuring and property shorthand in weather app

- Use destructuring in app.js, forecast.js, and geocode.js
- Use property shorthand in forecast.js and geocode.js
- Test your work and ensure the app still works

  ```js
  // app.js
  ...
  // destructure geoData, remove need for geoData object references
  geocode(location, (err, { latitude, longitude, location } = {})
  ...

  // forecast.js
  ...
  // destructure body for ease of use or w/e?
  request({ url, json: true }, (err, { body }) => {
  ...
  // This was, I think, way more sensible
  const { weather_descriptions, temperature, feelslike } = body.current
  ...

  // geocode.js
  ...
  // body again
  request({ url, json: true }, (err, { body }) => {
  ...
  // I still think this is better.
  const { center, place_name } = body.features[0]
  ```

### 041 - Bonus: HTTP Requests Without a Library

- Can do requests without request or a library, but they're a bit harder
- We can use modules Node provides
- This is an optional video
- We're going to use the HTTP and HTTPS
  - Yeah, it's annoying that there are separate modules. `request` abstracts this out.
  - Can use these to make new servers or make requests to existing servers
- `/playground/6-raw-http.js`

  ```js
  import http from 'http'

  import { weatherstackKey } from '../weather-app/keys.js'

  const url = `http://api.weatherstack.com/current?access_key=${weatherstackKey}&query=40,-75&units=f`

  let request = http.request(url, (res) => {
    let data = ``

    res.on(`data`, (chunk) => {
      data += chunk.toString()
      console.log(data + `\n\n`)
    })

    res.on(`end`, () => {
      const body = JSON.parse(data)
      console.log(body)
    })
  })

  request.on(`error`, (error) => {
    console.log(`Error: `, error)
  })

  request.end()
  ```

- This is kind of a PITA.
- It's better to use a library.

---

## Section 7: Web Servers

### 042 - Section Intro: Web Servers (Weather App)

- We're going to make web servers
- More available than a CLI
- We'll be using Express, the most popular web server out there
- Build a server, serve up assets, HTML, CSS, JS, JSON data, etc.

### 043 - Hello Express!

- We'll create our first Node server
- Now we can get a URL up to interact with the app
- We'll start with a server, then move to APIs
- Expressjs.com has docs and such
- Let's get a basic server up and running
- create `/web-server` at base of course folder
- Enter, npm init -y, npm i express
- Will also be using a `src` dir for first time here
- `/src/app.js`

  ```js
  // import express
  import express from 'express'

  // call express to start server
  const app = express()

  // .get takes route and fx for what to do
  // args are req object and res object
  app.get(`/`, (req, res) => {
    // Send something back to user
    res.send(`Hello express!`)
  })

  app.get(`/help`, (req, res) => {
    res.send(`Help has arrived!`)
  })

  // Set the server to listen on a prescribed port
  app.listen(`3000`)
  ```

- App will continue running until stopped because it's built to do so
- Can use `nodemon` to update on changes
- Can use `localhost:3000` to hit the site
- Routes that aren't built will 404, but we can do that later
- #### Challenge: Setup two new routes

  - Set up an about route and render a page title
  - Set up a weather route and render a page title
  - Test your work by visiting both in the browser

  ```js
  app.get(`/about`, (req, res) => {
    res.send(`About the app.`)
  })

  app.get(`/weather`, (req, res) => {
    res.send(`It will rain until this message changes.`)
  })
  ```

### 044 - Serving up HTML and JSON

- We have a server and routes set up
- We're more likely to send back HTML/JSON over strings
- We'll just change what we send in send()
- Can just put HTML in the string we send and it'll get wrapped in base tags
- Can send JSON by just sending an object or array
- #### Challenge: Update routes
  - Set up about route to render a title with HTML
  - Set up weather to send back JSON response
    - Use dummy object with forecast and location strings.
  - Test your work by visiting both in the browser
- `/web-server/src/app.js`

  ```js
  // import express
  import express from 'express'

  // call express to start server
  const app = express()

  // .get takes route and fx for what to do
  // args are req object and res object
  app.get(`/`, (req, res) => {
    // Send something back to user
    res.send(`<h1>Weather</h1>`)
  })

  app.get(`/help`, (req, res) => {
    res.send({
      name: `Corey`,
      age: 31,
    })
  })

  app.get(`/about`, (req, res) => {
    res.send(`<h1>About the App</h1>`)
  })

  app.get(`/weather`, (req, res) => {
    res.send({
      forecast: `The weather forecast is that there will be weather whether or not you can weather it.`,
      location: `Hatboro, PA`,
    })
  })

  // Set the server to listen on a prescribed port
  app.listen(`3000`)
  ```

- It'd be nice to just serve up an HTML file...

### 045 - Serving up Static Assets

- We've been messing with servers for a bit...
- It'd be great if we could send back an HTML file
- Let's configure Express to serve up an assets directory - `/web-server/public`
- Add an `index.html` in `public`
- We need an absolute path to the public folder.

  ```js
  // import express
  import express from 'express'
  import path from 'path'

  // modules workaround
  // requires path as well, but not doubly imported
  import { fileURLToPath } from 'url'
  const __filename = fileURLToPath(import.meta.url)
  const __dirname = path.dirname(__filename)
  // end workaround

  // gets the directory the filename lives in
  // console.log(`__dirname`, __dirname)
  // gets the same path with filename and extension
  // console.log(`__filename`, __filename)
  // gets the path for the public folder
  // console.log(`joined path`, path.join(__dirname, `..`, `/public`))

  // call express to start server
  const app = express()

  app.use(express.static(path.join(__dirname, `..`, `/public`)))
  ...
  ```

- Since index.html has special meaning, it overrides the route we built
- Express returns when it hits something that fits, then not anything after
- #### Challenge: Create two more HTML files
  - Create an HTML page for about
  - Create an HTML page for help
  - Remove the old route handlers
  - Test your work
  ***
  - Easy enough, just set up the pages (I made folders with index.html pages) and remove the routes.

### 046 - Serving Up CSS, JS, Images, and More

- Let's add CSS, JS, Images, etc.
- We can add CSS like normal. Add files, reference them as you do on a server, and it works.
- It all works like this.

### 047 - Dynamic Pages with Templating

- We have a static site. We want dynamic content!
- We're going to set up Handlebars!
  - Get dynamic documents
  - Create reusable code across pages
- `npm i hbs`
  - handlebars server
  - easy to use with a server like Express
- `` app.set(`view engine`, `hbs`) ``
  - Adds `hbs` as view engine
  - Expects views to be stored in `/web-server/views` directory
- `/web-server/views/index.hbs`

  ```js
  <html lang="en">
    <head>
      <meta charset="UTF-8" />
      <meta http-equiv="X-UA-Compatible" content="IE=edge" />
      <meta name="viewport" content="width=device-width, initial-scale=1.0" />
      <title>Document</title>
      <link rel="stylesheet" href="/styles/styles.css" />
    </head>
    <body>
      <h1>{{ title }}</h1>
      <p>{{ name }}</p>

      <script src="/scripts/script.js"></script>
    </body>
  </html>
  ```

- `/web-server/app.js`

  ```js
  app.get(``, (req, res) => {
    // first arg is view to render
    // second arg is dynamic values to pass
    res.render(`index`, {
      title: `Weather App`,
      name: `Corey Gross`,
    })
  })
  ```

- Similarly replace title and add a `Created By {{name}}` line for an About template.
- #### Challenge: Create a template for /help
  - Set up a help template to render a help message to the screen
  - Set up the help route and render the template with an example message
  - Visit the route in the browser and see your help message print

### 048 - Customizing the Views Directory

- Let's customize how HBS is set up
- We can change the location and name of the views dir
- Renaming to `templates` and refreshing any page shows why this sucks
- add `` viewsPath = path.join(__dirname, `../templates`) `` as a const to the app.js, use it in `` app.set(`views`, viewsPath) ``

### 049 - Advanced Templating

- Let's learn partials!
- Little templates that are parts of the webpage, like the header or footer.
- Gives a unified look without copying markup
- We'll load up hbs for the first time and work with it
- We're going to put our views in `/templates/views` and tell Node how that works by updating the path
- We'll add `/templates/partials`, a const for its path, and set it up with hbs using `hbs.registerPartials(partialsPath)`
- `/templates/partials/header.hbs`
  ```jsx
  <h1>Static Header.hbs Text</h1>
  ```
- Can load in on other pages using `{{>partial}}` syntax
- Nodemon won't load unless we adjust our nodemon call to `nodemon src/app.js -e js,hbs`
- Can replace static headers with header partial
- Can reference variables in the partial just like in normal templates
- #### Challenge: Create a partial for the footer
  - Set up the template for the footer partial "Created by Some Name"
  - Render the partial at the bottom of all three pages
  - Test your work by visiting all three pages
  - **This is really just repetition.**

### 050 - 404 Pages

- How do we set up a 404?
- `` app.get(`*`, ...) `` as last route
  - Express looks for matches in order listed. First, it looks for static pages. Then at our routes, including our final wildcard route.
- We can add subroutes, too.
- Something like this will work above the full wildcard:
  ```js
  app.get(`/help/*`, (req, res) => {
    res.send(`help article not found`)
  })
  ```
- #### Challenge: Create and render a 404 page with HBS
  - Set up template to render header and footer
  - Set up template to render an error message
  - Render the template for both 404 routes
  - Test your work. Visit /what and /help/units

### 051 - Styling the Application: Part I

- Let's style things!
- We'll focus on styles.css and index.hbs
- There's a lesson in here on styling, and the site gets styled

### 052 - Styling the Application: Part II

- Flexbox sticky footer
- Andrew's bad at proper HTML
- Adds favicons and titles for pages

## Section 8: Accessing API from Browser (Weather App)

### 053 - Section Intro: Accessing API from Browser

- We'll learn how to make an API and access it from the browser

### 054 - The Query String

- Query string lives as an object in req.query -`app.js`

```js
app.get(`/products`, (req, res) => {
  if (!req.query.search) {
    return res.send({
      error: `You must provide a search term`,
    })
  }
  res.send({
    products: [],
  })
})
```

- #### Challenge: Update weather endpoint to accept address

  - No address? Send an error message
  - Address? Send back the static JSON
    - Add address property onto JSON with the address
  - Test /weather and /weather?address=philadelphia

  ```js
  app.get(`/weather`, (req, res) => {
    if (!req.query.address) {
      return res.send({
        error: `You must provide a location.`,
      })
    }
    res.send({
      forecast: `The weather forecast is that there will be weather whether or not you can weather it.`,
      location: req.query.address,
    })
  })
  ```

### 055 - Building a JSON HTTP Endpoint

- Let's finalize the weather endpoint
- We have the geocode and weather code from weather-app
- We'll grab entire `utils` folder
- We'll also need to install `request`
- #### Challenge: Wire up /weather

  - Require geocode/forecast into app.js
  - Use the address to geocode
  - Use the coordinates to get forecast
  - Send back the real forecast and location
  - `app.js`

  ```js
  app.get(`/weather`, (req, res) => {
    if (!req.query.address) {
      return res.send({
        error: `You must provide a location.`,
      })
    }
    geocode(req.query.address, (geoErr, geoData) => {
      if (geoErr) {
        return res.send({
          error: geoErr,
        })
      } else {
        forecast(
          geoData.latitude,
          geoData.longitude,
          (forecastErr, forecastData) => {
            if (forecastErr) {
              return res.send({ error: forecastErr })
            } else {
              res.send({
                forecast: forecastData,
                location: geoData.location,
              })
            }
          }
        )
      }
    })
  })
  ```

- Data returned:
  ```json
  {
    "forecast": "Partly cloudy. It is currently 81 degrees out. It feels like 86 degrees out.",
    "location": "Philadelphia, Pennsylvania, United States"
  }
  ```

### 056 - ES6 Aside: Default Function Parameters

- Let's look at default parameters to add a default param and fix a problem with our code
- Playground File

  ```js
  const greeter = (name = `User`) => {
    console.log(`Hello ${name}`)
  }

  greeter(`Andrew`)

  greeter()
  ```

- He also covers destructuring with undefined items, and it errors out
- Additionally, he uses a number where an object is expected, and shows how given params overwrite defaults
- The problem with the server is the geocode call, where he destructured the geoData. Just add a default empty object.

### 057 - Browser HTTP Requests with Fetch

- We've got the endpoint! Let's focus on the front end...
- How do we show the data in the browser?
- Let's focus on `public` and `templates` in `web-server`
- We'll fetch the forecast on `index.hbs` using `public/scripts/script.js` and the `fetch` API
- #### Challenge: Fetch weather

  - Set up a call to fetch to fetch weather for Boston
  - Get the parsed JSON response
    - If it has an error, print the error
    - If not, print location and forecast
  - Refresh the browser and test your work

  ```js
  console.log(`script.js begin`)

  const puzzle = `http://puzzle.mead.io/puzzle`,
    weather = `/weather?address=Boston`

  fetch(puzzle).then((response) => {
    response.json().then((data) => {
      console.log(data)
    })
  })

  // Fetch at the URL, then() structure is promise
  // this then has a response, which we then use...
  fetch(weather).then((response) => {
    // ...after we jsonify it...
    response.json().then((data) => {
      // ... to do our application logic
      if (data.error) {
        return console.log(data.error)
      }
      return console.log(data)
    })
  })

  console.log(`script.js end`)
  ```

### 058 - Creating a Search Form

- We'll create a search form
- `index.hbs`
  ```html
  <form>
    <input type="text" placeholder="Location" />
    <button>Search</button>
  </form>
  ```
- `script.js`

  ```js
  const weatherForm = document.querySelector(`form`),
    search = document.querySelector(`input`)

  weatherForm.addEventListener(`submit`, (e) => {
    e.preventDefault()
    if (search.value === '') {
      console.log(`Must provide a location.`)
    } else {
      console.log(search.value)
      const weather = `/weather?address=${search.value}`
      fetch(weather).then((response) => {
        response.json().then((data) => {
          if (data.error) {
            return console.log(data.error)
          } else {
            console.log(data.location)
            console.log(data.forecast)
          }
        })
      })
    }
  })
  ```

### 059 - Wiring up the User Interface

- Let's get the messages from the console into the browser
- Let's provide a place to put them: two new paragraphs
- #### Challenge: Render content to paragraphs

  - Select the second message p from JS
  - Just before fetch, render loading message and empty p
  - If error, render error
  - If no error, render location and forecast.
  - Test your work!

  ```js
  console.log(`script.js begin`)

  const weatherForm = document.querySelector(`form`),
    search = document.querySelector(`input`),
    messageOne = document.getElementById(`messageOne`),
    messageTwo = document.getElementById(`messageTwo`)

  weatherForm.addEventListener(`submit`, (e) => {
    e.preventDefault()
    messageOne.textContent = `Loading...`
    messageTwo.textContent = ``
    if (search.value === '') {
      console.log(`Must provide a location.`)
    } else {
      console.log(search.value)
      const weather = `/weather?address=${search.value}`
      fetch(weather).then((response) => {
        response.json().then((data) => {
          if (data.error) {
            messageOne.textContent = data.error
            return console.log(data.error)
          } else {
            console.log(data.location)
            console.log(data.forecast)
            messageOne.textContent = data.location
            messageTwo.textContent = data.forecast
          }
        })
      })
    }
  })

  console.log(`script.js end`)
  ```

- Add a few styles and we're done
- Let's learn to deploy so other people can use our apps!

---

## Section 9: Application Deployment (Weather App)

### 060 - Section Intro: Application Deployment

- Right now, it runs locally. Great! But no one can use it.
- Let's deploy it publicly.
- We'll use git, GitHub, and Heroku

### 061 - Joining Heroku and GitHub

- He talks about git, GitHub, and Heroku, along with tours.
- We should install heroku CLI tools
- Heroku commands
  - heroku -v // Get version
  - heroku login // link to account
  - I have accounts and am used to both services, so the next two videos are probably not super useful

### 062 - Version Control with Git

- Heroku needs access to code to deploy, and GitHub needs it to exist
- We'll use git as our VCS
- He talks about the value of VCS
- Install git.

### 063 - Exploring Git

- Shows a basic flow of how git works with tracked, unstaged, staged, and commited changes
- All new files are untracked.
- Staged changes are prepared to be committed
- Commited changes are added to the repo
- Unstaged changes are changes on tracked/committed files

### 064 - Integrating Git

- We'll add git to the web server
- `git init` in `web-server`
- Explains `repositories/repos`
- Explains VSCode git support and `.git` being a hidden dir and how it should never be touched
- Explains `.gitignore` for `node_modules`
- `git add .`
- `git commit -m "Initial Commit"`
- Shows tracked changes for modified files
- This is all local
- Challenge is to put `notes-app` under VCS

### 065 - Setting up SSH Keys

- Sets up SSH keys for GitHub and Heroku
- `ls -al ~/.ssh`
  - Have id_rsa and id_rsa.pub? You're good
  - Otherwise, generate using `ssh-keygen -t rsa -b 4096 -C "email@address.tld"`
    - ssh-keygen -- generate keys
    - -t rsa -- type/protocol, here RSA protocol
    - -b 4096 -- bits, number of bits in key
    - -C "email@address.tld" -- comment/identifier/tag
    - Enters wizard, can use defaults
    - Run ls again to see keys
      - Keep id_rsa private, never share
      - id_rsa.pub is the public key, for limited use
    - eval "$(ssh-agent -s)" -- start ssh-agent with new keys
    - ssh-add ~/.ssh/id_rsa -- add identity with ssh key
      - use -K flag on mac

### 066 - Pushing Code to Github

- Create repo online
- Add remote
- Push to origin main
- My SSH is on GitHub. If it wasn't, I'd add it

### 067 - Deploying Node.js to Heroku

- We're going to push to heroku
- We'll set up a new remote and push
- `heroku keys:add` add heroku key to account
- `heroku create <name>` create heroku application
- add `"start": "node src/app.js"` (maybe with more flags if you're using experimental junk like I am) so Heroku knows how to start the app
- `app.js` port needs to change
  - `const port = process.env.PORT || 3000`
  - use port var instead of port number
- also need to change fetch URL to be root-relative
- Keys might get hairy here...
- Using dotenv is now... annoying. But doable.

### 068 - New Feature Deployment Workflow

- New feature workflow is moot, as I've implemented dotenv.
- We'll add data to the about page.
- #### Challenge: Add new data to forecast
  - Update forecast string to include new data
  - Commit changes
  - Push changes to GH and Heroku
  - Test work live
  - I added humidity.

### 069 - Avoiding Global Modules

- Creating development script with nodemon (I already had this as start-mon)
- dev only works because nodemon is global; we should add it locally as --save-dev/-D
- And that's the weather app!

---

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

---

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
  const
  ```

### 093 - Promise Chaining

### 094 - Promise Chaining Challenge

### 095 - Async/Await

### 096 - Async/Await: Part 2

### 097 - Integrating Async/Await

### 098 - Resource Updating Endpoints: Part I

### 099 - Resource Updating Endpoints: Part 2

### 100 - Resource Deleting Endpoints

### 101 - Separate Route Files

---

## Section 12: API Authentication and Security (Task App)

### 102 -

### 103 -

### 104 -

### 105 -

### 106 -

### 107 -

### 108 -

### 109 -

### 110 -

### 111 -

### 112 -

### 113 -

### 114 -

### 115 -

### 116 -

### 117 -

### 118 -

### 119 -

### 120 -

### 121 -

### 122 -

### 123 -

### 124 -

### 125 -

### 126 -

### 127 -

### 128 -

### 129 -

### 130 -

### 131 -

### 132 -

### 133 -

### 134 -

### 135 -

### 136 -

### 137 -

### 138 -

### 139 -

### 140 -

### 141 -

### 142 -

### 143 -

### 144 -

### 145 -

### 146 -

### 147 -

### 148 -

### 149 -

### 150 -

### 151 -

### 152 -

### 153 -

### 154 -

### 155 -

### 156 -

### 157 -

### 158 -

### 159 -

### 160 -

### 161 -

### 162 -

### 163 -

### 164 -

### 165 -

### 166 -

### 167 -

### 168 -

### 169 -

### 170 -

### 171 -

### 172 -

### 173 -

### 174 -

### 175 -

### 176 -

### 177 -
