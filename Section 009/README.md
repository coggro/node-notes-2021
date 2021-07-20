# The Complete Node.js Developer Course (3rd Edition)

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
    - git push origin heroku
  - Test work live
  - I added humidity.

### 069 - Avoiding Global Modules

- Creating development script with nodemon (I already had this as start-mon)
- dev only works because nodemon is global; we should add it locally as --save-dev/-D
- And that's the weather app!
