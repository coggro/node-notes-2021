# The Complete Node.js Developer Course (3rd Edition)

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
