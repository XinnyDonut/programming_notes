```
app.use(cors())                      <- handle cross-origin resource sharing,mainly for development
app.use(express.static('dist'))      <- serve static files from 'dist' directory(the react app)
app.use(express.json())              <- parsing incoming post/put request with JSON content to js obj
app.use(middleware.requestLogger)      <- customed middleware that logs request details  
app.use(middleware.tokenExtractor)     <- extract token with req.get() from Authorization header
app.use('/api/fish',fishRouter)        
app.use('/api/login',loginRouter)
app.use('/api/users',userRouter)
app.use('/api/logs',cookingLogRouter)
app.get('*', (req, res) => {           <- catch all remaining get request and serves index.html
  res.sendFile(path.join(__dirname, 'dist', 'index.html'))
})                            
app.use(middleware.unknownEndpoint)    <- runs if no route handled the request and return 404
app.use(middleware.errorHandler)
```
- ```express.json()```is a middleware that handles incoming requests with JSON data.
  - It checks if the incoming request has a Content-Type: application/json header
  - If it does, it parses the JSON string in the request body
  - It converts that JSON into a JavaScript object
  - It attaches this object to req.body so you can access it in your route handlers

- for example:

  ```
  // Client sends a POST request with this JSON body
  // {"name": "Salmon", "description": "Pink fish"}

  app.use(express.json())  // Enable the middleware

  app.post('/api/fish', (req, res) => {
    console.log(req.body.name)       // "Salmon"
    console.log(req.body.description) // "Pink fish"
    
    // Now you can use these values
    const newFish = new Fish({
      name: req.body.name,
      description: req.body.description
    })
  })
  ```