# The Complete Node.js Developer Course (3rd Edition)

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
