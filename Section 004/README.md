# The Complete Node.js Developer Course (3rd Edition)

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
