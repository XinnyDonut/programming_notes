```csharp
public class Player
{   
    // backing field
    private int _score;  

    // Full property implementation with custom getter
    public int Score
    {
        get { return _score; }
        //value keyword means value assigned to the property
        //automaticall available within setter block
        private set { _score = Math.Max(0, value); } // Can't go below 0
    }
    
    // Auto-implemented property 
    public string Name { get; set; }
    
    // Property with public getter but private setter 
    public int Level { get; private set; }
    
}

// Usage:
Player player = new Player();
player.Name = "John";           // Works - public getter and setter
int level = player.Level;       // Works - public getter
// player.Level = 5;           // Won't work - private setter
int score = player.Score;       // Works - public getter
```

- readonly is a field modifier that can only be applied to fields, not properties. It specifies that the field can only be assigned a value

    - During declaration
    - In the constructor of the same class

- private is an access modifier that can be applied to:

    - Fields
    - Methods
    - Properties
    - Nested types
    - Events

- Notice the difference between field modifier and access modifier

```csharp
public class Example
{
    // Private readonly field - can only be set at declaration or in constructor
    private readonly int _maxAttempts = 3;
    
    // Private field - can be changed anywhere within the class
    private string _currentStatus;
    
    // Private property - has private setter, public getter
    public string Status { get; private set; }
    
    // Private method
    private void UpdateInternalState() { }
    
    public Example(int customMaxAttempts)
    {
        // Valid - can set readonly field in constructor
        _maxAttempts = customMaxAttempts;
    }
    
    public void SomeMethod()
    {
        // ERROR - Cannot modify readonly field after construction
        // _maxAttempts = 5;
        
        // Valid - can modify private field
        _currentStatus = "Working";
        
        // Valid - can use private setter within class
        Status = "Active";
    }
}
```