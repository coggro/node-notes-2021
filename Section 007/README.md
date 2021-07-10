# The Complete Node.js Developer Course (3rd Edition)

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
