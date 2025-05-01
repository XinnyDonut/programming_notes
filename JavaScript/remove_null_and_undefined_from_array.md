```typescript
const domains = includeUrls.map(urlObj => {
    try {
        const url = new URL(urlObj.url);
        return url.hostname;
    } catch (error) {
        return null;
    }
}).filter(Boolean) as string[];
//`filter(Boolean) is a way to remove null or undefined value from an array

//this is to convert the domains array to set to remove duplicated ones
const uniqueDomains = [...new Set(domains)];
```
###### Understanding `.filter(Boolean)`
The `.filter(Boolean)`part is a concise way of removing null or undefined values from an array. When you pass the Boolean function as a callback to filter, it:

- Converts each item to a boolean value
- Only keeps items that convert to true

Since null converts to false, any URLs that failed to parse (and returned null in the try/catch block) will be removed.
###### Understanding [...new Set(domains)]
This creates a new array with only unique values from domains:

- new Set(domains) creates a Set object, which automatically removes duplicates
- The spread operator `...`iterates through each value in the Set, the sread operator basically means interating.
- Each value is placed as a separate element in the new array being created with `[...]`

This is a common way to get an array of unique values in JavaScript/TypeScript.
It's equivalent to doing something like:
```javascript
const domainsSet = new Set(domains);
const uniqueDomains = [];
for (const domain of domainsSet) {
    uniqueDomains.push(domain);
}
```
##### Understanding as `as String[]` typescript assertion
The as string[] type assertion tells TypeScript that the result will be an array of strings, which is needed because TypeScript can't automatically infer that the filter operation has removed all potential nulls.
