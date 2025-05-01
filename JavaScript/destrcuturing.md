
##### Example 1 -desctucture with renaming

```javascript
const {
    data: assistant,
    isLoading: isLoadingAssistant,
    error: assistantError,
    refetch
} = useGetAssistant(assistantId || '');
```
```javascript
function Profile({ name: userName, age: userAge }) {
  return <h1>{userName} is {userAge} years old</h1>;
}
```
##### Example 2 - destructur with providing Default Values
```javascript
function Profile({ name = "Guest", age = 18 }) {
  return <h1>{name} is {age} years old</h1>;
}

<Profile />; // Renders: "Guest is 18 years old"
```