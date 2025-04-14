When you see code like this:

```csharp
var webCrawlRun = new AssistantWebCrawlRun
{
    Id = Guid.NewGuid(),
    AssistantId = assistantId,
    CreatedAtUtc = currentTime,
    LastActivityAtUtc = currentTime,
    CreatedByUserId = user.UserProfileId,
    Status = WebCrawlerStatus.Pending
};
```

This is actually doing two things in sequence:

- First, it calls the default constructor `AssistantWebCrawlRun()`(the one you defined at the beginning of your class)
- Then, it sets the properties specified in the curly braces

So what's happening behind the scenes is:

- The constructor creates the object and initializes the collections:`AssistantWebCrawlUrlRuns = new HashSet<AssistantWebCrawlUrlRun>()`
- Then the object initializer assigns values to the specific properties you listed inside the curly braces
