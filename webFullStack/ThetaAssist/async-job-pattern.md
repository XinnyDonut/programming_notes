# Asynchronous Job Pattern in Web Applications

## Overview

The Asynchronous Job Pattern (also known as "Background Job Pattern") is an architectural approach for handling long-running operations in web applications. Unlike synchronous request-response patterns where clients wait for operations to complete, this pattern allows the server to acknowledge requests immediately while processing work in the background.

## Key Components

1. **Immediate Acknowledgment**: Server quickly returns a job ID or reference
2. **Background Processing**: Work happens asynchronously after the response
3. **Status Tracking**: Client can check job status using the ID
4. **Completion Notification**: Client learns of job completion through polling or push notifications

## Implementation in Theta Assist Web Crawler

Theta Assist implements this pattern for web crawling operations which could take minutes or hours to complete.

### 1. Client-Side Request (React/TypeScript)

When a user initiates a web crawl, the application makes an API call but doesn't wait for the crawl to complete:

```typescript
// From useStartWebCrawl.ts
const startWebCrawl = useStartWebCrawl();

// Inside a component:
const handleStartCrawl = () => {
    // Validation checks...
    
    startWebCrawl.mutate(
        {assistantId, bypassRobotsTxtCheckDomains},
        {
            onSuccess: runId => {
                // Navigate to details page with the returned ID
                navigate(
                    `/assistants/${assistantId}/edit/webcrawl/history/${runId}`
                );
            },
            onError: err => {
                setError(`Failed to start web crawl: ${err.message}`);
            }
        }
    );
};
```

### 2. Server-Side API Endpoint (C#)

The API endpoint creates a job record and returns its ID without waiting for completion:

```csharp
// From WebCrawlEndpoints.cs
app.MapPost("/api/assistants/{assistantId}/webcrawl/start", async (
        EndpointExecutor executor,
        AssistantWebCrawlService webCrawlService,
        ILogger<AssistantWebCrawlService> logger,
        HttpContext context,
        string assistantId,
        StartWebCrawlRequest request,
        CancellationToken cancellationToken) =>
{
    return await executor.ExecuteServiceResultAsync(
        (user) => webCrawlService.StartWebCrawlAsync(user, assistantId, request, cancellationToken),
        logger,
        context
    );
})
.RequireAuthorization("UpnDomainPolicy");
```

### 3. Service Layer Implementation (C#)

The service creates a job record and returns just the ID:

```csharp
// From AssistantWebCrawlService.cs
public async Task<ServiceResult<Guid>> StartWebCrawlAsync(
    UserProfileModel user,
    string assistantId,
    StartWebCrawlRequest request,
    CancellationToken cancellationToken)
{
    // Validation checks...

    // Create a new web crawl run with Pending status
    var webCrawlRun = new AssistantWebCrawlRun
    {
        Id = Guid.NewGuid(),
        AssistantId = assistantId,
        CreatedAtUtc = currentTime,
        LastActivityAtUtc = currentTime,
        CreatedByUserId = user.UserProfileId,
        Status = WebCrawlerStatus.Pending
    };
    
    // Add URL runs...
    
    // Save to database
    dbContext.AssistantWebCrawlRuns.Add(webCrawlRun);
    await dbContext.SaveChangesAsync(cancellationToken);

    // Return only the ID - work will happen in background
    return webCrawlRun.Id.ToSuccessResult();
}
```

### 4. Background Processing (C#)

A separate background service picks up pending jobs and processes them:

```csharp
// From WebCrawlProcessorService.cs
public async Task ProcessWebCrawlAsync(Guid webCrawlRunId, CancellationToken stoppingToken)
{
    // Get crawl run data...
    
    // Update status to Processing
    await UpdateCrawlStatusAsync(webCrawlRunId, WebCrawlerStatus.Processing, "Starting web crawl...", stoppingToken);
    
    // Perform the actual crawling...
    foreach (var webCrawlUrlRun in includedUrlRuns)
    {
        // Process each URL...
        await webCrawler.CrawlAsync(/*...*/);
    }
    
    // Update status to Completed when done
    await UpdateCrawlStatusAsync(webCrawlRunId, WebCrawlerStatus.Completed, "Web crawl completed.", stoppingToken);
}
```

### 5. Status Updates (C#)

The background process updates the job status in the database:

```csharp
private async Task UpdateCrawlStatusAsync(Guid crawlRunId, WebCrawlerStatus status, string logMessage, CancellationToken stoppingToken)
{
    // Create a new scope and DbContext for the status update
    using var scope = _serviceProvider.CreateScope();
    var dbContext = scope.ServiceProvider.GetRequiredService<ApplicationDbContext>();

    var currentTime = DateTime.UtcNow;

    // Apply status update
    await dbContext.AssistantWebCrawlRuns
        .Where(r => r.Id == crawlRunId)
        .ExecuteUpdateAsync(r => r
            .SetProperty(x => x.Status, status)
            .SetProperty(x => x.LastActivityAtUtc, currentTime)
            .SetProperty(x => x.CompletedAtUtc, status == WebCrawlerStatus.Completed || 
                                                status == WebCrawlerStatus.Error || 
                                                status == WebCrawlerStatus.Cancelled ? 
                                                DateTime.UtcNow : (DateTime?)null),
            stoppingToken);

    // Append log message
    await AppendLogAsync(crawlRunId, logMessage, stoppingToken);
}
```

### 6. Polling for Status (React/TypeScript)

The client polls for updates using the job ID:

```typescript
// From WebCrawlDetailsPage.tsx
const {
    data: runDetails,
    isLoading,
    error,
    refetch
} = useGetCrawlRunDetails(assistantId, crawlRunId);

// Set up auto-refresh for in-progress runs
useEffect(() => {
    if (
        runDetails &&
        (runDetails.status === WebCrawlerStatus.Pending ||
         runDetails.status === WebCrawlerStatus.Processing)
    ) {
        // Create new interval to check for updates
        refreshIntervalRef.current = setInterval(() => {
            refetch();
        }, 5000); // Refresh every 5 seconds
    } else if (refreshIntervalRef.current) {
        // Clear interval if run is complete
        clearInterval(refreshIntervalRef.current);
        refreshIntervalRef.current = null;
    }

    // Cleanup
    return () => {
        if (refreshIntervalRef.current) {
            clearInterval(refreshIntervalRef.current);
            refreshIntervalRef.current = null;
        }
    };
}, [runDetails, refetch]);
```

### 7. Job Cancellation Support

The pattern also supports cancellation:

```typescript
// Client-side cancellation in WebCrawlDetailsPage.tsx
const handleCancelCrawl = () => {
    closeConfirmModal();
    cancelCrawlMutation.mutate(
        {
            assistantId,
            webCrawlRunId: crawlRunId
        },
        {
            onSuccess: () => {
                refetch();
            }
        }
    );
};
```

```csharp
// Server-side cancellation in WebCrawlProcessorService.cs
private async Task<bool> IsCrawlCancelledAsync(Guid webCrawlRunId, CancellationToken stoppingToken)
{
    using var scope = _serviceProvider.CreateScope();
    var dbContext = scope.ServiceProvider.GetRequiredService<ApplicationDbContext>();

    return await dbContext.AssistantWebCrawlRuns
        .Where(r => r.Id == webCrawlRunId && r.Status == WebCrawlerStatus.Cancelled)
        .AnyAsync(stoppingToken);
}

// Regular checks in processing loop
if (await IsCrawlCancelledAsync(webCrawlRunId, stoppingToken))
{
    await AppendLogAsync(webCrawlRunId, "Crawl was cancelled by user. Stopping processing.", stoppingToken);
    break;
}
```

## Benefits of This Pattern

1. **Improved Responsiveness**: The server responds quickly to requests
2. **Better Resource Utilization**: Resources are allocated efficiently for long-running tasks
3. **Enhanced User Experience**: Users can continue working while tasks process
4. **Resilience**: Background tasks can be retried or resumed if they fail
5. **Scalability**: The system can manage many concurrent long-running jobs

## Considerations When Implementing

1. **Job Persistence**: Background jobs must be stored durably to survive server restarts
2. **Progress Tracking**: Detailed progress information helps users understand what's happening
3. **Resource Management**: Consider limiting concurrent jobs to prevent overload
4. **Error Handling**: Robust error handling for background tasks is essential
5. **Cleanup**: Implement mechanisms to clean up old job data

This pattern is essential for any web application that needs to handle operations that take longer than a typical HTTP request timeout.
