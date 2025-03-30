**Use Proxy in development**
```
// vite.config.js
export default {
  server: {
    proxy: {
      '/api': {
        target: 'http://localhost:3001',
        changeOrigin: true
      }
    }
  }
}
```
- this tells Vite, when you see a request to ```/api```, forward it to ```http://localhost:3001/api```
- without it, it would go to ```http://localhost:5173/api``` which will fail

**In production**
- Express server serves the React app's static files (HTML, CSS, JS) when someone visits your site
- Express server also Handles API requests to endpoints like  ```/api/fish```
- So when your built React app is running in the browser and makes a request to ```/api/fish```, that request goes to the same server that delivered the React app in the first place.
`
**different ways of path**
- Root-relative Path: ```const baseUrl = '/api/fish'```, the leading slash tells the browserto start at the root of whatever doman the app is currently running
- Path-relative Path: without the leading slash,for example, ```api/fish``` is relative to the current path,for example, if I'm on ``` https://example.com/products/shoes``` and my code requests to ```api/fish```without the slash, it will go to ```https://example.com/products/shoes/api/fish```

**distinguish between browser URL and api endpoints**
- FrontEnd:
  - Your React code running in the browser
  - Displays UI, handles user interactions
  - Makes requests to get/send data
- Backend:
  - Your Express.js server running on a server
  - Processes requests, interacts with database
  - Sends responses back to the frontend
- API endpoints:
  - Specific URLs on your backend that accept requests
  - Example: ```/api/fish``` is an endpoint to get fish data
- Browser Navigation URLs (what users see and use)
  - Example: https://yoursite.com/fish/3
  - Handled by React Router in your frontend
  - These determine what page/component to show to the user
  - These don't directly connect to your backend
- API Request URLs (what axios uses)
  - Example: /api/fish/3
  - Used by your frontend code to request data from your backend
  - Users never directly type these in the address bar
  - Processed by your Express routes

**Example of the flow**
1. User visits https://yoursite.com/fish/3 in their browser
2. React Router shows the FishDetails component for fish with ID 3
3. The FishDetails component runs this code:
4. javascriptCopyaxios.get('/api/fish/3')
5. This sends a request to https://yoursite.com/api/fish/3 (a backend endpoint)
6. Your Express backend processes this request and sends back data
