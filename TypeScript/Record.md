
```typescript
const [domainSettings, setDomainSettings] = useState<Record<string, boolean>>({});
```
- `Record<T,K>`create a single object, where keys are type T and values are type K. The previous code would look like this
```javascript
{
  "example.com": true,
  "another-site.com": false
}
```

- If backend data model is a dictionary/map structure (like a C# `Dictionary<string, bool>`), then using `Record<string, boolean> `in TypeScript would be a very appropriate mapping.