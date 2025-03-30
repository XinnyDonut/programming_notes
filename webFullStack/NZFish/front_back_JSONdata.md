**How frontedn send data to backend**
1. User Side:
  - User fills out a form in your React app
  - The form data is stored in JavaScript objects/state
  - When submitted, Axios converts these JavaScript objects to JSON
  - Axios sends this JSON to your server with Content-Type: application/json
2. Server Side:
  - The JSON string arrives at your Express server
  - express.json() middleware intercepts and parses it
  - It converts the JSON string back to JavaScript objects
  - It makes these objects available at req.body
  - Your route handler can now work with the data as JavaScript objects 
  - if needed return the a plain json data with ```res.json()```

***Example***

```
// FRONTEND: React component
const [name, setName] = useState('Snapper')
const [description, setDescription] = useState('Delicious fish')

const handleSubmit = async (e) => {
  e.preventDefault()
  
  // JavaScript object
  const fishData = { name, description }
  
  // Axios converts to JSON and sends
  await axios.post('/api/fish', fishData)
}

// BACKEND: Express route
app.post('/api/fish', async (req, res) => {
  // express.json() has already parsed the JSON back to a JavaScript object
  const { name, description } = req.body
  
  // Now you can use it
  const fish = new Fish({ name, description })
  await fish.save()
})
```
**How frontend receive backend data**
1. Express responds with a JSON payload using ```res.json()```
  - Express is using JSON.stringify() behind the scene
   
2. Axios receives the HTTP response from the server, it automatically parses the JSON response body into a JavaScript object. This parsed data is made available as the data property of the``` response ```object.
 ```
  const getAllFish = async () => {
    const response=await axios.get(baseUrl)
    return response.data
  }
  ```
  - the complete Axios response object typically contains several properties:
    - data: The parsed response body (converted from JSON to a JavaScript object)
    - status: The HTTP status code (e.g., 200, 404)
    - statusText: The HTTP status message (e.g., "OK", "Not Found")
    - headers: The HTTP headers of the response
    - config: The original request configuration
    - request: The request object
  - So when we access ```response.data```, you're getting the already-parsed JavaScript object that was originally sent as JSON from your server. This automatic parsing is a convenient feature of Axios that saves you from having to manually call ```JSON.parse()``` on the response.

