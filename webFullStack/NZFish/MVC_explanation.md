**My current backend:**
```
nz-fish/
├── controllers/
  ├── CookinglogRoute.js
  ├── fishRoute.js
  ├── loginroute.js
  └── userRoute.js
├── models/
  ├── cookingLog.js
  ├── fish.js
  ├── user.js
├── utils/
│   ├── middleware.js
│   ├── config.js
│   ├── logger.js
│   └── seedFish.js
└── app.js
├── requestes/
└── package.json
```
- My controller is double dutying- handle both HTTP layer(req,res) and the business logic(what would normally in a service layer)
- My model is primairly define the data strcuture, while business logic(service)lives in my controllers
- My validation only happends in the the mongoose schemas


- A traditional MVC(Model-View-Controller) structor would look like:
```
nz-fish/
├── src/
│   ├── controllers/      <- request handler
│   ├── models/           <- data structure
│   ├── services/         <- business logic
│   ├── validators/       <- Data validation rules
│   ├── utils/
│   │   ├── middleware/
│   │   ├── errors/
│   │   ├── logger.js
│   │   └── seedFish.js
│   ├── config/
│   ├── docs/
│   └── app.js
├── tests/
└── package.json
```
- in traditional MVC, Model is everything related to data and business logic,including schema,services,valadation
- though Model folder will mainly contains the schema/datastructure files
- in Express application, use express-valaditor for validator layer