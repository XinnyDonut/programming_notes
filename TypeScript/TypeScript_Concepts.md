### TypeScript Concepts
##### 1. Type Annotations
```typescript
//the type is followed by :
const name: string = '';
//| is union type says the variable can only be one of those values or null
//the =null part is just to initialize the variable with null
const reasoningEffort: 'low' | 'medium' | 'high' | null = null;
// if used in function, it means the function's return type. Below function return type is number
function add(a: number, b: number): number {
    return a + b;
}


```
##### 2. Interfaces - define the shape of obj
```typescript
//define the shape of ModifyAssistant obj

interface ModifyAssistantRequest {
  //? means that property is optional
  name?: string;
  description?: string;
  instructions?: string;
  // other properties...
}
```
##### 3.Generics
```typescript
//tells that this useState hook works with string
const [name, setName] = useState<string>('');
//tells what type the modifyRequest gonnabe
const modifyRequest = useMemo<returnType>(() => {...}, [...]);
```
`useState`and `useMemo`is generic functions and `<>` defineds the type

```typescript
function useMemo<T>(factory: () => T, deps: DependencyList): T;
```
- <T>: This is a generic type parameter, meaning useMemo can work with any type.

- factory: () => T: The function you pass to useMemo must return something of type T.

- deps: DependencyList: The dependency array that determines when to recompute.

- : T: This means the function itself returns a value of ty
