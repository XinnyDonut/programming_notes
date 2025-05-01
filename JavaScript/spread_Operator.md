##### Example 1
```javascript
const obj1 = { name: "Alice" };
const obj2 = { age: 25 };
const merged = { ...obj1, ...obj2 }; // { name: "Alice", age: 25 }
```
##### Example 2
```javascript
const [user, setUser] = useState({ name: "Alice", age: 25 });

setUser((prev) => ({ ...prev, age: 26 })); // Only updates 'age', keeps other properties
```

##### Example 3 use `...` in array
```javascript
// Let's say we have a Set of domains
const domainsSet = new Set(['example.com', 'test.org', 'sample.net']);

// Using the spread operator with array brackets
const domainsArray = [...domainsSet];
// What happens behind the scenes:
// 1. JavaScript sees the spread operator and starts iterating through the Set
// 2. It takes each value and places it as an element in the new array
// 3. This creates: ['example.com', 'test.org', 'sample.net']
```
##### Example 4 use `...` in function calls
```javascript
const domainsSet = new Set(['example.com', 'test.org', 'sample.net']);

function showDomains(first, second, third) {
    console.log(`Domains: ${first}, ${second}, ${third}`);
}

// Using spread in function arguments doesn't need brackets
showDomains(...domainsSet);
// This calls: showDomains('example.com', 'test.org', 'sample.net')
```