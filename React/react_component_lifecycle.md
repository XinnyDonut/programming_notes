### React Component LifeCycle

#### Key LifeCycle Phases

1. Mounting(Component Creation)

   When a component is first added to the DOM:

   ```jsx
   // Component is created and mounted
   <Route path="threads/:threadId" element={<ThreadDetail />} />
   ```

   During mounting:

   - Component function executes for the first time
   - Initial state is created with useState
   - Refs are initialized with useRef(initialValue)
   - useEffect hooks with any dependency array run after render
   - DOM elements are created and attached to the document

2. Updataing(Props/State Changes)

   When props change or state updates:

   ```jsx
   // Component updates when messages change
   setMessages([...messages, newMessage]);
   ```

   During updates:

   - Component function executes again
   - State from useState persists with updated values
   - Refs from useRef maintain their values
   - useEffect hooks run based on their dependency arrays,cleanup function will run first before the effect

3. Unmounting (Component Removal)
   When a component is removed from the DOM:

   ```jsx
   // Component unmounts when navigating away
   navigate(`/assistants/${assistantId}/threads/${newThreadId}`);
   ```

   During unmounting:

   - Cleanup functions from useEffect hooks run
   - DOM nodes created by the component are removed
   - Internal React state for the component is destroyed
   - Refs are effectively deleted along with the component instance

#### React Router's Caomponent Instance Behavior

When you have a route configuration like:

```jsx
<Route path="threads/:threadId" element={<ThreadDetail />} />
```

React Router follows these principles:

- Fresh Start Principle: Each unique URL deserves a fresh component instance with clean state
- Complete Lifecycle: Components get full mount/unmount lifecycle events when routes change
- Parameter Isolation: Changes in route parameters (like :threadId) trigger complete remounting
