```
require('dotenv').config()

const PORT=process.env.PORT||3001
const MONGODB_URL=process.env.MONGODB_URI

module.exports = {
  PORT,
  MONGODB_URL
}
```

- ```process.env``` is actually part of Node.js's global ```process```object
- ```process.env``` contains all environmental viriables from the system/runtime environment, such as```process.env.Path```, ```process.env.HOME```,we don't need ```dotenv.config()```to access these
- what``` dotenv.config()``` does is to read the ```.env```file, parse it to key value pairs, and add these pairs to Node's already existing ```process.env```obj, so that we can access the environment variable we defined in ```.env``` file
