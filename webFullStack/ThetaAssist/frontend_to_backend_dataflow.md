### frontend to backend data flow

1. Frontend Object (TypeScript/JavaScript)

```typescript
const domainsToRespect = {
  "example.com": true,
  "google.com": false,
};
```

2. JSON Serialization and HTTP rquest

```typescript
// In your API call function
const body = JSON.stringify({ domainSettings: domainsToRespect });
// Results in: {"domainSettings":{"example.com":true,"google.com":false}}

await authenticatedFetch(
  `${baseUrl}/api/assistants/${assistantId}/webcrawl/start`,
  {
    method: "POST",
    headers: { "Content-Type": "application/json" },
    body: body, // The serialized JSON string
  }
);
```

3. ASP.NET Core deserialization

```csharp
// In WebCrawlEndpoints.cs
app.MapPost("/api/assistants/{assistantId}/webcrawl/start", async (
    // ...
    WebCrawlStartRequest request,  // ASP.NET automatically deserializes JSON to this class
    // ...
) => {
    // ...
});

// In WebCrawlStartRequest.cs
public class WebCrawlStartRequest
{
    public Dictionary<string, bool> DomainSettings { get; set; } = new();
}
```

### Explaination

```typescript
// Frontend
const body = JSON.stringify({ domainSettings: domainsToRespect });
```

```csharp
// Backend model
public class WebCrawlStartRequest
{
    public Dictionary<string, bool> DomainSettings { get; set; } = new();
}
```

1. ASP.NET CORE sees you have a parameter of type WebCrawlStartRequest
2. It tries to deserialize the entire request body into that type
3. It matches JSON properties to the properties defined in the class
4. The property names must match (ASP.NET Core binding is case-insensitive, so "domainSettings" in JSON matches "DomainSettings" in C#).
5. TypeScript `Record<string, boolean>` maps perfectly to a C# `Dictionary<string, bool>`. This is a common pattern for passing key-value data between frontend and backend because they serialize to identical JSON structures.

### Complete Data Flow

Complete Data Flow

1. UI Interaction:

- User fills out the form, selects domain settings in the modal
- A React component calls the React Query mutation hook

2. Frontend API Call :

- Request data (domain settings) get JSON stringified
- Transported in the HTTP request body
- Request URL includes the assistantId in the path

3. Backend Request Processing ((API layer)):

- Request arrives at the endpoint
- ASP.NET Core performs model binding right at this point (before service layer)
- Route parameter binding extracts `assistantId` from URL
- Request body binding deserializes JSON to `WebCrawlStartRequest`

4. Service Layer Processing:

- Endpoint calls `AssistantWebCrawlService.StartWebCrawlAsync`
- Service method creates new database entities (AssistantWebCrawlRun)
- Domain settings need to be stored in the database here
- Returns run ID to frontend

5. Background Processing:

- `WebCrawlProcessorService` picks up the pending job
- It needs to read the domain settings from the database
- Passes settings to `WebCrawler.CrawlAsync`
- Crawler conditionally checks robots.txt based on settings
