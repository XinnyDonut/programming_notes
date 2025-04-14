#### Explicit Return

```javascript
setDomainSettings(
  extractedUniqueDomains.map((d) => {
    return { domain: d, isRespected: true };
  })
);
```

Here, the arrow function has a function body with curly braces {}, and inside that body, you explicitly use the return keyword to specify what value should be returned.

#### Implicit Return

With implicit return, the return keyword is omitted, and the value after the arrow is automatically returned:

```javascript
setDomainSettings(
  extractedUniqueDomains.map((d) => ({ domain: d, isRespected: true }))
);
```

The key difference here is:

- No function body with curly braces
- No return keyword
- Parentheses () around the object literal

The parentheses around the object are necessary in implicit returns because without them, JavaScript/TypeScript would interpret the curly braces as the function body rather than an object literal:

```javascript
// This doesn't work - JavaScript thinks the { } is a function body
setDomainSettings(extractedUniqueDomains.map(d => { domain: d, isRespected: true }));
```
