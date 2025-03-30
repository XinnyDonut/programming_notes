### Nullish Coalescing `??`
- Returns the right-hand value only if the left-hand value is null or undefined.
- Unlike`||`, it doesn’t treat`0`, `false`, or `""` as falsy.

```javascript
const value = null ?? "default";
console.log(value); // "default"

const num = 0 ?? 42;
console.log(num); // 0 (not 42, because 0 is not null or undefined)
```
