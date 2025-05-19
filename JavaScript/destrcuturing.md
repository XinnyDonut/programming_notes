##### Example 1 -desctucture with renaming

```javascript
const {
  data: assistant,
  isLoading: isLoadingAssistant,
  error: assistantError,
  refetch,
} = useGetAssistant(assistantId || "");
```

```javascript
function Profile({ name: userName, age: userAge }) {
  return (
    <h1>
      {userName} is {userAge} years old
    </h1>
  );
}
```

##### Example 2 - destructur with providing Default Values

```javascript
function Profile({ name = "Guest", age = 18 }) {
  return (
    <h1>
      {name} is {age} years old
    </h1>
  );
}

<Profile />; // Renders: "Guest is 18 years old"
```

##### Example 3 - Nested destructuring

```javascript
const response = {
  status: 200,
  user: {
    name: "John Doe",
    email: "john@example.com",
    age: 30,
    address: {
      street: "123 Main St",
      city: "New York",
    },
  },
  timestamp: 1621234567890,
};

const { user } = response;
// Now user = { name: "John Doe", email: "john@example.com", ... }

// Then you'd need another line to get properties from user
const { name, email } = user;
// Now name = "John Doe" and email = "john@example.com"

const {
  user: { name, email },
} = response;
// do this in one step with nested destructuring
// this create 2 variable
//name with value "John Doe"
//email with value "john@example.com"
```

even deeper nesting:

```javascript
const {
  user: {
    name,
    address: { city },
  },
} = response;
// name = "John Doe"
// city = "New York"
```

combine with other destructuring:

```javascript
const {
  status,
  user: { name, email },
  timestamp,
} = response;
// status = 200
// name = "John Doe"
// email = "john@example.com"
// timestamp = 1621234567890
```
