##### Example 1
```javascript
const obj1 = { name: "Alice" };
const obj2 = { age: 25 };
const merged = { ...obj1, ...obj2 }; // { name: "Alice", age: 25 }
```
##### Example 2
```javascript
const [user, setUser] = useState({ name: "Alice", age: 25 });

setUser((prev) => ({ ...prev, age: 26 })); // Only updates 'age', keeps other properties
```
##### Example 3 -spread operator with renaming

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
##### Example 4 - Providing Default Values
```javascript
function Profile({ name = "Guest", age = 18 }) {
  return <h1>{name} is {age} years old</h1>;
}

<Profile />; // Renders: "Guest is 18 years old"
```