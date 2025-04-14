1. CommonJS(cjs) import--tradional Node.js

```javascript
// Importing
const express = require("express");
const { Router } = require("express");
const myModule = require("./myModule");

// Exporting
module.exports = myRouter;
module.exports.myFunction = () => {};
// OR
exports.myFunction = () => {};
```

2. ES Module(ESM)--Modern JavaScript

```javascript
// Importing
import express from "express";
import { Router } from "express";
import myModule from "./myModule";
import { someFunction, otherFunction } from "./utils";
import * as allUtils from "./utils";

// Exporting
export default myRouter;
export const myFunction = () => {};
// OR
export { myFunction, otherFunction };
```
