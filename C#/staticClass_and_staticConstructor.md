- A static class in C# is a class that cannot be instantiated and can only contain static members. It's marked with the static keyword.
```
public static class MathUtilities
{
    public static double PI = 3.14159;
    
    public static int Add(int a, int b)
    {
        return a + b;
    }
    
    public static int Multiply(int a, int b)
    {
        return a * b;
    }
}
```
- there is no need to create instance when using static class
```
// No need to create an instance
int sum = MathUtilities.Add(5, 3);
double circleArea = MathUtilities.PI * radius * radius;
```

- A static constructor is used to initialize static members of a class. It's called automatically before the first instance is created or any static members are referenced. Non static class can have static constructors
```
public class Database
{
    private static string connectionString;
    
    // Static constructor
    static Database()
    {
        connectionString = "Server=myserver;Database=mydb;User Id=username;Password=password;";
        Console.WriteLine("Static members initialized");
    }
    
    // Instance constructor
    public Database()
    {
        Console.WriteLine("Database instance created");
    }
    
    public void Connect()
    {
        Console.WriteLine($"Connecting using: {connectionString}");
    }
}
```
- Static classes cannot be instantiated - You cannot use the new keyword with them
- Static classes can only contain static members - All methods, fields, properties must be static
- Static constructors run only once - Before any static member is accessed or instance is created
- Static constructors have no access modifiers - They cannot be public, private, etc.
- Static constructors cannot take parameters
- Static constructors cannot be called directly - They're invoked automatically by the CLR
- Thread safety - The CLR guarantees that static constructors are thread-safe
- Use cases - Utility classes, extension methods, and configuration settings are common use cases