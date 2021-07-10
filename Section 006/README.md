# The Complete Node.js Developer Course (3rd Edition)

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
