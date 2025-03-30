### Optional Chaining `?`
- Allows you to safely access deeply nested properties without checking each level.
- If a property is null or undefined, it short-circuits and returns undefined instead of throwing an error.
##### Example 1
In the expression `assistant?.id`:
- If assistant exists (is not null or undefined), it will access the id property normally
- If assistant is null or undefined, instead of throwing an error, it will return undefined

This is extremely useful when dealing with data that might not be loaded yet. 
```javascript
// With optional chaining
const id = assistant?.id;

// Without optional chaining,we need to write
const id = assistant && assistant.id;
```

##### Example 2
```javascript
const user = { profile: { name: "Alice" } };
console.log(user.profile?.name); // "Alice"
console.log(user.profile?.age);  // undefined
console.log(user.address?.street); // undefined (no error!)
```