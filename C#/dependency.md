#### Class dependency
- there are constructor injection,property injection and method injection
- Class dependencies are external components that a class needs to perform its operations. In object-oriented programming, classes rarely work in isolation—they collaborate with other classes to accomplish their tasks.
- class dependencies are best injected through consturctor
```csharp
public class CustomerService
{
    private readonly ICustomerRepository _repository;
    private readonly ILogger _logger;
    private readonly IEmailService _emailService;
    
    public CustomerService(
        ICustomerRepository repository, 
        ILogger logger, 
        IEmailService emailService)
    {
        _repository = repository ?? throw new ArgumentNullException(nameof(repository));
        _logger = logger ?? throw new ArgumentNullException(nameof(logger));
        _emailService = emailService ?? throw new ArgumentNullException(nameof(emailService));
    }
    
    public void RegisterCustomer(Customer customer)
    {
        _logger.LogInfo("Registering new customer");
        _repository.Add(customer);
        _emailService.SendWelcomeEmail(customer.Email);
    }
}
```
- Compare with : Property injection involves setting dependencies through public properties after the object is constructed.**
```csharp
public class CustomerService
{
    public ICustomerRepository Repository { get; set; }
    public ILogger Logger { get; set; }
    public IEmailService EmailService { get; set; }
    
    public void RegisterCustomer(Customer customer)
    {
        // Need to check for null since properties might not be set
        if (Repository == null || Logger == null || EmailService == null)
            throw new InvalidOperationException("Required dependencies not set");
            
        Logger.LogInfo("Registering new customer");
        Repository.Add(customer);
        EmailService.SendWelcomeEmail(customer.Email);
    }
}
```
**Advantages of Constructor Injection**

- Required Dependencies: Makes it impossible to create an object without providing required - dependencies
- Immutable References: Dependencies can be stored in readonly fields
- Clear Contract: The constructor clearly defines what the class needs
- Fail Fast: Objects fail at creation time if dependencies are missing, not when a method is called
- Clean State: Objects are always in a valid state after construction

#### Method dependency

```csharp
// Traditional class-level dependency injection
public class ReportGenerator 
{
    private readonly IDataProvider dataProvider;
    
    public ReportGenerator(IDataProvider dataProvider)
    {
        this.dataProvider = dataProvider;
    }
    
    public Report GenerateReport(string reportId)
    {
        var data = dataProvider.GetData(reportId);
        return new Report(data);
    }
}
// Method-level dependency injection
public class ReportGenerator 
{
    public Report GenerateReport(string reportId, Func<string, Data> getDataFunction)
    {
        var data = getDataFunction(reportId);
        return new Report(data);
    }
}
// Usage
var report = reportGenerator.GenerateReport("sales-2023", id => dataProvider.GetData(id));
```
- `Func<>` is a generic delegate type in C# that represents a function with specific parameter types and a return type. The last type parameter always represents the return type, and any parameters before it represent the input parameter types.
Let's break down these examples:

1. Above example, `Func<string, Data>` represents a function that:
    - Takes a single parameter of type string
    - Returns a value of type Data   

2. Func<Task<string>> represents a function that:
    ```csharp
    public async Task<string> GetOrFetchRobotsTxtAsync(
        string domain,
        Func<Task<string>> fetchAction,
        CancellationToken cancellationToken
    )
    ```
    - Takes no parameters (there are no types before the return type)
    - Returns a Task<string> (an asynchronous operation that will eventually produce a string)

#### Optional Dependency

```csharp
public class Logger
{
    private readonly IMetricsCollector? metricsCollector;
    
    public Logger(IMetricsCollector? metricsCollector = null)
    {
        this.metricsCollector = metricsCollector;
    }
    
    public void LogMessage(string message)
    {
        Console.WriteLine(message);
        
        // Null check before using the optional dependency
        if (metricsCollector != null)
        {
            metricsCollector.IncrementCounter("LoggedMessages");
        }
    }
}
// You can create a Logger without providing the metrics collector
var simpleLogger = new Logger();

// Or with a metrics collector
var advancedLogger = new Logger(new AzureMetricsCollector());
```
The syntax `IMetricsCollector? metricsCollector = null` is declaring an optional parameter in a C# method or constructor. It combines several C# features:

- Optional Parameter: The `= null` part makes this parameter optional by providing default value `null` meaning you can call the method/constructor without providing this argument. If no argument is provided when calling the method, the parameter will automatically be assigned the value `null`
- Nullable Reference Type: The question mark `?`after IMetricsCollector indicates that this is a nullable reference type, which is a feature introduced in C# 8.0. It explicitly states that this parameter can be null.
- Interface Type: IMetricsCollector is an interface type, indicating that any class implementing this interface can be passed.

Together, this syntax means:

The parameter is optional, defaulting to null if not provided
The compiler and IDE will help warn about potential null reference exceptions
Static analysis tools will understand that this parameter might be null