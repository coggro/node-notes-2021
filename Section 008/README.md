# The Complete Node.js Developer Course (3rd Edition)

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
