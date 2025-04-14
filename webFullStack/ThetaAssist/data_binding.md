```csharp
app.MapPost("/api/assistants/{assistantId}/webcrawl/start", async (
    EndpointExecutor executor,  // <-Service binding (DI)
    string assistantId,       // <-Route parameter binding
    WebCrawlStartRequest request, // <-Request body binding
    CancellationToken cancellationToken //<-Special token binding
) => {
    // Use all bound parameters
});
```

1. Service Binding (Dependency Injection)

EndpointExecutor executor is provided by ASP.NET Core's dependency injection system
The framework looks for a registered service of type EndpointExecutor in your application's service container
It automatically creates or retrieves an instance and passes it to your method
This happens completely on the server side with no involvement from the frontend

2. Special Framework-Provided Values

CancellationToken cancellationToken is a special parameter automatically populated by ASP.NET Core
The framework creates a cancellation token linked to the HTTP request's lifecycle
If the client disconnects or the request times out, this token will be cancelled
It enables your long-running operations to be cancellable when the client drops the connection

These parameters are examples of ASP.NET Core's powerful parameter binding system extending beyond just HTTP request data. The framework can provide:

- HTTP request data (route values, query strings, headers, body)
- Services from the dependency injection container
- Special framework-provided values (HttpContext, CancellationToken)
- Custom-bound values through model binding providers
