```
app.use(express.static('dist'))

app.get('*', (req, res) => {
  res.sendFile(path.join(__dirname, 'dist', 'index.html'))
})
```

- the ```app.get()``` is to ensure when user refresh a pge, it doesn't cause 404 error
- '*' catches all routes,if user ask for any page that's not an API route or static file, serve the react app and let react figure out what to show
- _dirname is Node.js global variable,which contains the absolute path of the directory containing currently executing file
- path.join() is Node.js utility that joins path segments,that handles different operating system, connecting pieces of path with correct separator

**How SPA works**
1. Initial Load:
  - Browser request ```nzfish.onrender.com```
  - Server sends the complete React app(index.html +JS bundles)
  - React initializes and renders the homepage showing the fish list
2. When user clicks a fish:
  - React Router captures this click
  - It changes the URL in the browser (to /fish/123) without a full page reload
  - React unmounts the fish list component and mounts the fish details component
  - Your FishDetails component makes an API request to get just the data it needs
  - The page updates without reloading
3. when user log in:
  - User enters credentials in the Login component
  - Your app makes an API request to /api/login
  - The server responds with a token
  - React stores this token (in state and localStorage)
  - React updates the UI to show the logged-in state (changing NavBar appearance)
  - React might redirect to another route (like the homepage)
  - All without a full page reload!

**Key difference**
- Traditional websites: Each click = new HTML page from server
- SPAs: One initial HTML load, then JavaScript updates the content dynamically
- The server is only contacted when you need data:
  - Fetching fish details from your API
  - Submitting login credentials
  - Posting a new cooking log