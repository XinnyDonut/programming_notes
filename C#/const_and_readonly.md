**const must be assigned when declared,**
```
public const int MaxUsers = 100;

// This is not allowed
public const int MinUsers; // Error: Constant fields must be assigned
MinUsers = 1; // Error: Cannot assign to const field
```

**Constants have these key characteristics:**
- They must be assigned a value at declaration time,once assigned, it cannot be changed
- Their values can't be changed after compilation
- They're evaluated at compile time, not runtime
- They can only be initialized with:
  - Primitive types (int, double, bool, etc.)
  - String literals
  - Other constants
  - Simple expressions involving constants

**const vs readonly
- Unlike const, readonly fields can be assigned at runtime and in constructors:
```
// Readonly can be assigned in constructor
public readonly int MaxUsers;

public MyClass()
{
    MaxUsers = 100; // Valid for readonly, not for const
}
```
- Compile-time vs. Runtime: Constants are evaluated at compile time, making them slightly more performant but less flexible than readonly fields, readonly is determined at runtime
- Type Limitations: Constants can only be primitive types or strings, while readonly can be any type, including complex obj
- Memory Allocation: Constants are embedded directly in the IL code, readonly references are stroed in memory at runtime
- Access Modifiers: Constants are implicitly static (you can't use the static keyword with them).
