# The Complete Node.js Developer Course (3rd Edition)

## Section 15: Sending Emails (Task App)

### 130 - Section Intro: Sending Emails

- Let's learn to set up email notificatons
- We'll be able to just call a function from SendGrid (like Postmark and Mandrill)
  - NOPE! Mailgun

### 131 - Exploring SendGrid

- Let's explore SendGrid, send an email from Node, and set up emails for created accounts and deleted accounts
- SendGrid.com has a significant free tier
- For the most part, we'll use the web api code that Mailgun provides
- Install the npm package for the service `npm i @sendgrid/mail`
- ~~For now, let's put the API key in the code. In a few videos, we'll put it in the env~~ SendGrid now has this in their default example code

  ```js
  import sendgrid from '@sendgrid/mail'

  sendgrid.setApiKey(process.env.SENDGRID_API_KEY)

  const data = {
    from: `MYEMAIL`,
    to: `MYOTHEREMAIL`,
    subject: `Hello`,
    text: `Testing some Mailgun awesomeness!`,
  }

  sendgrid
    .send(data)
    .then(() => {
      console.log(`Email sent`)
    })
    .catch((e) => {
      console.error(e)
    })
  ```

- This does it, as annoying as it is

### 132 - Sending Welcome and Cancelation Emails

- Let's integrate emails into the Task Manager app with Welcome and Cancellation emails
- `account.js` will stay where it is with email functions that get called elsewhere
- He teaches template strings with text in this email
- `account.js`

  ```js
  import sendgrid from '@sendgrid/mail'

  sendgrid.setApiKey(process.env.SENDGRID_API_KEY)

  const sendWelcomeEmail = (email, name) => {
    // This is async! We could await here or in user, but it doesn't
    // make sense to do so. We could do an HTML email, but text is
    // fine for now. Text has a higher open and response rate anyway.
    sendgrid.send({
      to: email,
      from: `MYEMAIL`,
      subject: `Thanks for joining in!`,
      text: `Welcome to the app, ${name}. Let me know how you get along with the app.`,
    })
  }

  export { sendWelcomeEmail }
  ```

- `/routers/user.js`

  ```js
  ...
  // Import welcome email function
  import { sendWelcomeEmail } from '../email/account.js'
  ...
  router.post(`/users`, async (req, res) => {
    const user = new User(req.body)
    try {
      await user.save()
      // Send Welcome Email
      sendWelcomeEmail(user.email, user.name)
      const token = await user.generateAuthToken()

      res.status(201).send({ user, token })
    } catch (e) {
      res.status(400).send(e)
    }
  })
  ```

- He shows how to register a domain. Doesn't seem necessary for me quite yet.
- #### Challenge: Create cancellation email fx

  - Set up a new fx for sending an email on cancellation
    - email and name as args
    - Include their name in the email and ask why they cancelled
    - Call it just after the account is removed
    - Run the request and check your inbox!

  ```js
  // Easy peasy. Basically copy/paste. Just put this in the delete route
  // after the error check.
  const sendCancellationEmail = (email, name) => {
    sendgrid.send({
      to: email,
      from: `MYEMAIL`,
      subject: `Sorry to see you go!`,
      text: `What did we do that hurt you so? Let us know how we can do better!`,
    })
  }
  ```

### 133 - Environmental Variables

- Don't keep API keys and stuff in your repo where other people can see it
- Let's use environment variables
- Let's look at `db/mongoose.js`
- Heroku will be looking locally for our db, and it won't be there
- Let's use an env var and adjust between environments
- It'll also be more secure when we remove API keys
- His friend built a bot and publicized the API key in old commits and they ended up having to create a new Discord
- Don't commit .env files to retain security
- He does a config folder with dev.env
- If we set `PORT=3000` in this file, we can use `env-cmd` to get our env vars
- Same with the SendGrid API key, JWT Secret, and MongoDB Connection String
- #### Challenge: Pull JWT secret and DB URL into env vars
  - Create two new env vars: JWT_SECRET and MONGODB_URL
  - Set up values for each in the development env files
  - Swap out three hardcoded values
  - Test your work. Create a new user and get their profile
  - This is a great time to change the JWT secret that I already committed!
- This is stupidly simple. Not worth noting.

### 134 - Creating a Production MongoDB Database

- Starting the process of deploying to Heroku
- We'll use a MongoDB hosting solution from Mongo called Atlas
- We'll create a free cluster and let it get created over a few minutes...
- Follow the wizard with 0.0.0.0/0 and a new user
- We'll use Compass for the connection (also free, like Robo3T) and connect to local and production dbs
- Follow the wizards for the most part. It's pretty simple.
- Since we can connect, we're set! Neat!
- SRV records are DNS things

### 135 - Heroku Deployment

- Let's deploy to production on Heroku!
- We no longer need playground. We can then push the repo up
- Make sure to ignore config and node_modules
- We can set env vars with `heroku config:set key=value` and unset with `config:unset key`
- If we're sure to set JWT_SECRET, MONGODB_URL, and
