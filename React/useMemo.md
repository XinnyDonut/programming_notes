#### what is `useMemo`
- `useMemo`is ReactHook that memorize the result of a computation.
>Memoization" is a programming technique that stores the results of expensive function calls and returns the cached result when the same inputs occur again.

```javascript
const memoizedValue = useMemo(() => computeExpensiveValue(a, b), [a, b]);
```
This hook has two parts:
1. A function that computes a value
2. A dependency array`[a,b]`

#### how `useMemo`workings under the hood
1. When your component first renders, React runs the function inside useMemo and stores both the result and the current values of all dependencies.
2. On subsequent renders, React compares each dependency's current value with its value during the previous render (using Object.is comparison).
3. If none of the dependencies have changed, React skips running the function again and just returns the stored result - this is the "memoization" in action.
4. If any dependency has changed, React runs the function again, stores the new result along with the new dependency values, and returns the new result.

#### Examples with different return types
##### 1.Memorizing an obj
```javascript
const memoizedUserProfile = useMemo(() => {
  return {
    fullName: `${firstName} ${lastName}`,
    initials: `${firstName[0]}${lastName[0]}`,
    age: calculateAge(birthDate)
  };
}, [firstName, lastName, birthDate]);
```
##### 2. Memorizing an expensive calculation
```javascript
const memoizedStatistics = useMemo(() => {
  // This could be expensive with large datasets
  return {
    average: data.reduce((sum, item) => sum + item, 0) / data.length,
    max: Math.max(...data),
    min: Math.min(...data),
    median: calculateMedian(data)
  };
}, [data]);
```
##### 3.Memorizing Filtered/Sorted Arrays
```javascript
const filteredAndSortedItems = useMemo(() => {
  return items
    .filter(item => item.category === selectedCategory)
    .sort((a, b) => a.name.localeCompare(b.name));
}, [items, selectedCategory]);