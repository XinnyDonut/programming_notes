### passing functions vs calling functions

This is the wrong way of passing a function, this is calling a function
```jsx
onChange={handleDomainToggle(domain)} // ❌ This executes immediately during render!
```
The correct way: passing a function reference
```jsx
 const handleDomainToggle = (domain: string) => {
        if (selectedDomains.includes(domain)) {
            onDomainsChange(selectedDomains.filter((d: string) => d !== domain));
        } else {
            onDomainsChange([...selectedDomains, domain]);
        }
    }

onChange={() => handleDomainToggle(domain)} // ✅ Creates a function to be called later
```

`() => handleDomainToggle(domain)` is an anonymoous function reference. Because of javascript scope, it remembers which domain it's assoicated with.When the checkbox is changed, React calls this anonymous function, which then calls `handleDomainToggle` with the correct domain.

If the function doesn't need any parameters beyond the event objects React provides automatically, we can pass the function directly:
```jsx
// For a function that doesn't need parameters beyond the event:
<button onClick={handleClick}>Click Me</button>

// The function definition might look like:
const handleClick = (event) => {
  console.log('Button was clicked');
  console.log('Event:', event); // React provides this automatically
};
```
### three simple exmamples to clarify
- Example 1: Function with No Additional Parameters
```jsx
function Counter() {
  const [count, setCount] = useState(0);
  
  // This function doesn't need any parameters beyond the event React provides
  const increment = () => {
    setCount(count + 1);
  };
  
  return (
    <button onClick={increment}>Increment</button> // ✅ Direct function reference is fine
  );
}
```
- Example 2: Function That Needs Additional Parameters
```jsx
function ShoppingList() {
  const [items, setItems] = useState(['Apple', 'Banana', 'Cherry']);
  
  // This function needs to know which item to remove
  const removeItem = (itemToRemove) => {
    setItems(items.filter(item => item !== itemToRemove));
  };
  
  return (
    <ul>
      {items.map(item => (
        <li key={item}>
          {item}
          <button onClick={() => removeItem(item)}>Remove</button> // ✅ Anonymous function wrapper
        </li>
      ))}
    </ul>
  );
}
```
- Example 3: Accessing the Event Object with Parameters
```jsx
function Form() {
  const handleInputChange = (fieldName, event) => {
    console.log(`Field ${fieldName} changed to: ${event.target.value}`);
  };
  
  return (
    <form>
      <input 
        type="text" 
        onChange={(event) => handleInputChange('username', event)} 
      />
    </form>
  );
}
```
- Example 4: Common Mistake: Event Object Handling
```jsx
// If you need the event object:
onChange={(event) => handleDomainToggle(domain, event)}

// The handler would then look like:
const handleDomainToggle = (domain, event) => {
  console.log('Checkbox for', domain, 'changed to:', event.target.checked);
  // Rest of your logic...
};
```