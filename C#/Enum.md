**basic Enum example**
- Enums (short for enumerations) are a special type in C# that represent a set of named constants. They're useful when you need to define a set of related values that don't change during the execution of your program.
```
public enum DaysOfWeek
{
    Monday,
    Tuesday,
    Wednesday,
    Thursday,
    Friday,
    Saturday,
    Sunday
}

// Using the enum
public class Program
{
    public static void Main()
    {
        DaysOfWeek today = DaysOfWeek.Wednesday;
        
        if (today == DaysOfWeek.Saturday || today == DaysOfWeek.Sunday)
        {
            Console.WriteLine("It's the weekend!");
        }
        else
        {
            Console.WriteLine("It's a weekday.");
        }
    }
}
```
**By default, enum members are assigned integer values starting from 0. You can explicitly assign different values:**

```
public enum HttpStatusCode
{
    OK = 200,
    Created = 201,
    BadRequest = 400,
    Unauthorized = 401,
    NotFound = 404,
    InternalServerError = 500
}

public static void CheckResponse(HttpStatusCode statusCode)
{
    if (statusCode == HttpStatusCode.OK)
    {
        Console.WriteLine("Request successful");
    }
    else if ((int)statusCode >= 400)
    {
        Console.WriteLine($"Error occurred: {statusCode}");
    }
}
```

**Cannot Add Values at Runtime: Unlike some dynamic languages, enum values in C# must be defined at compile time.**