**config obj in axios**
```javascript
const config = ()=>{
  const token=authService.getToken()
  return {
    headers:
      {Authorization: Bearer ${token}}  
  }
}

const create =async (newLog) => {
  const response= await axios.post(baseUrl,newLog,config())
  return response.data
}
```
- When you call an Axios method like axios.post(), you can provide three parameters:
 - The URL to send the request to
 - The data to send in the request body
 - A configuration object with additional options
- ```configuration```obj structure is defined by what Axios expects for its HTTP request configuration, it can have various properties such as ```url```,```method```,```data```,```params```and ```headers```, in this case I used ```headers```
  - It can include properties like headers, params, timeout, etc.
  - The headers property specifically needs to be an object where each key is a header name and each value is that header's value
    - ```headers``` is a property of the config object that Axios uses to set HTTP headers
    - The value of ```headers``` is another object containing all the headers you want to send
    - Inside this nested object, ```Authorization``` is a specific HTTP header name
    - The value for this header follows the Bearer token format: ```"Bearer [your-token]"```
- When Axios sends an HTTP request with your config object, it converts those JavaScript objects into actual HTTP headers in the request. This happens at the network protocol level.

**How Express process the header axios send**
- Then on Express side, express receives the raw HTTP request
  - The request object in Express provides methods to access these HTTP headers
  - request.get('authorization') is a method that directly accesses the HTTP headers from the request
  - The request.get() method in Express is specifically designed to retrieve HTTP headers (case-insensitive), so request.get('authorization') works the same as request.get('Authorization').
- So Express doesn't need to know about your Axios config structure at all. By the time the request reaches your server:
  - Axios has already converted your config object into standard HTTP headers
  - Express simply reads these standard HTTP headers from the request

**Two ways of provide data in Axios request**
1. when using method-specific functions like ```axios.post()```, ```axios.put()```,the syntax is
```axios.post(url, data, config)```
  - First parameter is the URL
  - Second parameter is the data (request body)
  - Third parameter is the additional config (headers, etc.)
2. when using generic axios() function, we can also include everything in the config obj.
```
axios({
  url: baseUrl,
  method: 'post',
  data: newLog,
  headers: { Authorization: `Bearer ${token}` }
})
```
- Both approaches do exactly the same thing - they're just different syntaxes that Axios provides
- The ```axios.post(baseUrl, newLog, config()) ``` approach is clearer because it separates the main concerns (where to send the request and what data to send) from the configuration details (authentication headers).