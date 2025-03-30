- there are constructor injection,property injection and method injection**
- Class dependencies are external components that a class needs to perform its operations. In object-oriented programming, classes rarely work in isolation—they collaborate with other classes to accomplish their tasks.**
- class dependencies are best injected through consturctor**
```
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
```
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