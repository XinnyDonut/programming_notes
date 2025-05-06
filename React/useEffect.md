### Understand Cleanup in useEffect

In `useEffect`, the function you return is always the cleanup function, and it follows a specific execution order:

#### When cleanup func runs

1. Before the effect runs again - If the dependencies change and the effect needs to run again, React will first call the cleanup function from the previous run
2. When the component unmounts - When the component is about to be removed from the UI

```
Component mounts
│
└─▶ Effect runs for the first time
    │
    │   [Dependencies change]
    │
    ├─▶ Cleanup function runs
    │
    └─▶ Effect runs again
        │
        │   [Component unmounts]
        │
        └─▶ Cleanup function runs one final time
```

#### Simple Example

```javascript
useEffect(() => {
  console.log("🔵 Effect running");

  return () => {
    console.log("🔴 Cleanup running");
  };
}, [dependency]);
```

If we change the dependency value, we see the sequenece in our console:

```javascript
// Initial render
🔵 Effect running

// When dependency changes
🔴 Cleanup running
🔵 Effect running

// When component unmounts
🔴 Cleanup running
```

An eventListener typically follows this structure:

```javascript
useEffect(() => {
  // Setup phase
  element.addEventListener("event", handler);

  // Cleanup phase
  return () => {
    element.removeEventListener("event", handler);
  };
}, [dependencies]);
```
