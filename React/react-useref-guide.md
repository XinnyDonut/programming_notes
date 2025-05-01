# React useRef: A Practical Guide with Examples

## 1. What is useRef?

`useRef` is a React Hook that gives you a persistent reference object that stays the same between renders. The `.current` property of this object can be modified and accessed without causing re-renders.

### Basic Example

```jsx
import { useRef } from 'react';

function Counter() {
    // Initialize a ref with value 0
    const countRef = useRef(0);
    
    function handleClick() {
        // Update the ref
        countRef.current = countRef.current + 1;
        console.log(`You clicked ${countRef.current} times`);
    }
    
    return (
        <button onClick={handleClick}>
            Click me
        </button>
    );
}
```

Note that changing `countRef.current` doesn't trigger a re-render, unlike state updates.

## 2. Accessing DOM Elements

The most common useRef use case is getting a reference to a DOM element.

```jsx
function TextInputWithFocusButton() {
    // Create a ref with initial value null
    const inputRef = useRef(null);
    
    function focusInput() {
        // Access the DOM node
        inputRef.current.focus();
    }
    
    return (
        <>
            {/* Attach the ref to the input element */}
            <input ref={inputRef} type="text" />
            <button onClick={focusInput}>
                Focus the input
            </button>
        </>
    );
}
```

## 3. Storing Previous Values

Track a value from the previous render:

```jsx
function Counter() {
    const [count, setCount] = useState(0);
    const prevCountRef = useRef();
    
    useEffect(() => {
        // After render, save current count as "previous"
        prevCountRef.current = count;
    });
    
    // On first render, prevCountRef.current is undefined
    const prevCount = prevCountRef.current;
    
    return (
        <div>
            <h1>Now: {count}, before: {prevCount ?? 'N/A'}</h1>
            <button onClick={() => setCount(count + 1)}>
                Increment
            </button>
        </div>
    );
}
```

## 4. Solving Closure Problems

useRef is perfect for accessing current state in callbacks that might have captured stale values:

```jsx
function DelayedMessage() {
    const [message, setMessage] = useState('');
    const messageRef = useRef('');
    
    // Keep ref updated with latest state
    useEffect(() => {
        messageRef.current = message;
    }, [message]);
    
    function showMessageLater() {
        // Schedule an alert to happen after 3 seconds
        setTimeout(() => {
            // By using the ref, we get the CURRENT message
            // not the message value when this function was created
            alert(`Current message: ${messageRef.current}`);
        }, 3000);
    }
    
    return (
        <div>
            <input 
                value={message} 
                onChange={e => setMessage(e.target.value)} 
                placeholder="Type something"
            />
            <button onClick={showMessageLater}>
                Show message after 3s
            </button>
        </div>
    );
}
```

## 5. Managing Timers and Intervals

useRef helps with managing timers that need to be cleaned up:

```jsx
function Stopwatch() {
    const [time, setTime] = useState(0);
    const [isRunning, setIsRunning] = useState(false);
    
    // Store interval ID in a ref
    const intervalRef = useRef(null);
    
    // Start the stopwatch
    function startTimer() {
        if (isRunning) return;
        
        setIsRunning(true);
        intervalRef.current = setInterval(() => {
            setTime(time => time + 1);
        }, 1000);
    }
    
    // Stop the stopwatch
    function stopTimer() {
        if (!isRunning) return;
        
        clearInterval(intervalRef.current);
        setIsRunning(false);
    }
    
    // Clean up interval on unmount
    useEffect(() => {
        return () => {
            if (intervalRef.current) {
                clearInterval(intervalRef.current);
            }
        };
    }, []);
    
    return (
        <div>
            <p>Time: {time} seconds</p>
            <button onClick={startTimer} disabled={isRunning}>Start</button>
            <button onClick={stopTimer} disabled={!isRunning}>Stop</button>
        </div>
    );
}
```

## 6. Avoiding Unnecessary Re-renders

Track values without triggering re-renders:

```jsx
function MouseTracker() {
    // State for position (causes renders when updated)
    const [position, setPosition] = useState({ x: 0, y: 0 });
    
    // Ref for tracking mouse movement count (doesn't cause renders)
    const moveCountRef = useRef(0);
    
    useEffect(() => {
        function handleMouseMove(e) {
            // Update moveCount without causing re-renders
            moveCountRef.current += 1;
            
            // Only update state (and trigger re-render)
            // every 10th mouse movement
            if (moveCountRef.current % 10 === 0) {
                setPosition({ x: e.clientX, y: e.clientY });
            }
        }
        
        window.addEventListener('mousemove', handleMouseMove);
        return () => {
            window.removeEventListener('mousemove', handleMouseMove);
        };
    }, []);
    
    return (
        <div>
            <p>Position: {position.x}, {position.y}</p>
            <p>Total movements: {moveCountRef.current}</p>
        </div>
    );
}
```

## 7. Comparing useRef vs useState

Understanding when to use each:

```jsx
function RefVsStateExample() {
    // Re-renders component on change
    const [stateCount, setStateCount] = useState(0);
    
    // Doesn't cause re-renders
    const refCount = useRef(0);
    
    function incrementState() {
        setStateCount(stateCount + 1);
    }
    
    function incrementRef() {
        refCount.current += 1;
        console.log(`Ref count: ${refCount.current}`);
        // Notice: UI doesn't update until next render
    }
    
    return (
        <div>
            <p>State count: {stateCount}</p>
            <p>Ref count: {refCount.current}</p>
            
            <button onClick={incrementState}>Update State</button>
            <button onClick={incrementRef}>Update Ref</button>
            
            <button onClick={() => alert(`Ref: ${refCount.current}, State: ${stateCount}`)}>
                Show Latest Values
            </button>
        </div>
    );
}
```

## 8. Implementing a Form with Auto-save

Debouncing input with useRef:

```jsx
function AutoSaveForm() {
    const [text, setText] = useState('');
    const [isSaved, setIsSaved] = useState(true);
    
    // Store timeout ID in a ref
    const timeoutRef = useRef(null);
    
    function handleChange(e) {
        setText(e.target.value);
        setIsSaved(false);
        
        // Clear any existing timeout
        if (timeoutRef.current) {
            clearTimeout(timeoutRef.current);
        }
        
        // Set a new timeout to save
        timeoutRef.current = setTimeout(() => {
            console.log('Saving:', e.target.value);
            setIsSaved(true);
        }, 1000);
    }
    
    // Clean up timeout when component unmounts
    useEffect(() => {
        return () => {
            if (timeoutRef.current) {
                clearTimeout(timeoutRef.current);
            }
        };
    }, []);
    
    return (
        <div>
            <textarea
                value={text}
                onChange={handleChange}
                placeholder="Start typing..."
            />
            <p>{isSaved ? 'Saved' : 'Saving...'}</p>
        </div>
    );
}
```

## 9. Best Practices for useRef

1. **Initialize with a sensible value**:
   ```jsx
   // For DOM elements
   const elementRef = useRef(null);
   
   // For values
   const countRef = useRef(0);
   ```

2. **Update refs in event handlers or effects, not during rendering**:
   ```jsx
   // Good: Updating in an effect
   useEffect(() => {
       prevValueRef.current = value;
   }, [value]);
   
   // Good: Updating in event handler
   function handleClick() {
       countRef.current += 1;
   }
   
   // Bad: Updating during render
   function MyComponent() {
       const countRef = useRef(0);
       countRef.current += 1; // Don't do this - causes problems!
       // ...
   }
   ```

3. **Clean up timers and intervals stored in refs**:
   ```jsx
   useEffect(() => {
       // Setup...
       
       return () => {
           clearTimeout(timeoutRef.current);
           clearInterval(intervalRef.current);
       };
   }, []);
   ```

4. **Use state for values that should trigger re-renders**:
   ```jsx
   // If changes should update UI, use state
   const [isVisible, setIsVisible] = useState(false);
   
   // If changes shouldn't update UI, use ref
   const timeoutRef = useRef(null);
   ```

## 10. When to Choose useRef

- ✓ Accessing/manipulating DOM elements
- ✓ Storing values that don't affect rendering
- ✓ Persisting values between renders
- ✓ Fixing stale closures in callbacks
- ✓ Managing timers and intervals
- ✓ Storing previous state values
- ✓ Optimizing performance (avoiding unnecessary re-renders)

## Summary

`useRef` is a crucial React hook for managing values that need to persist between renders without causing re-renders. It's especially valuable for DOM access, fixing closure issues, and managing non-UI state.

Remember that changes to `ref.current` don't trigger re-renders, so use state for values that should affect your component's UI. Use refs when you need to track or store information without affecting rendering.
