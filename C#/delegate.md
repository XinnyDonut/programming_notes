```csharp
public class Button
{
    // This is a delegate type
    public event EventHandler Click;
    
    public void OnClick()
    {
        Click?.Invoke(this, EventArgs.Empty);
    }
}

// Usage
var button = new Button();
button.Click += (sender, e) => Console.WriteLine("Button clicked!");
```
- In `public event EventHandler Click`
 - `EventHandler` is the type - it's a delegate type provided by .NET
 - `Click` is the name of the event
 - `event` is a keyword that modifies how the delegate can be used

A delegate is essentially a type-safe function pointer. EventHandler is a built-in delegate type that represents a method that takes a sender object and event arguments.
