# JavaScript Timers & React Hooks Notes

## 1. What is `setTimeout()`?

`setTimeout()` executes a function **once** after a specified delay.

### Syntax

```javascript
setTimeout(callback, delay);
```

### Example

```javascript
setTimeout(() => {
  console.log("Hello");
}, 3000);
```

### Output

```text
(After 3 seconds)
Hello
```

---

## Internal Working of `setTimeout()`

JavaScript is single-threaded.

`setTimeout()` is not part of JavaScript itself. It is provided by:

* Browser Web APIs
* Node.js Runtime APIs

### Execution Flow

```text
Call Stack
    ↓
Web APIs
    ↓
Callback Queue
    ↓
Event Loop
    ↓
Call Stack
```

### Example

```javascript
console.log("Start");

setTimeout(() => {
  console.log("Timer");
}, 2000);

console.log("End");
```

### Output

```text
Start
End
Timer
```

### Why?

1. `console.log("Start")` executes.
2. `setTimeout()` registers a timer in Web APIs.
3. `console.log("End")` executes.
4. After 2 seconds, callback enters Callback Queue.
5. Event Loop moves callback to Call Stack.
6. Callback executes.

---

## 2. What is `setInterval()`?

`setInterval()` executes a function repeatedly after a fixed interval.

### Syntax

```javascript
setInterval(callback, delay);
```

### Example

```javascript
setInterval(() => {
  console.log("Hello");
}, 1000);
```

### Output

```text
Hello
Hello
Hello
Hello
...
```

Runs forever until stopped.

---

## Internal Working of `setInterval()`

1. Browser registers interval timer.
2. After every delay, callback is pushed to Callback Queue.
3. Event Loop moves callback to Call Stack.
4. Callback executes.
5. Process repeats.

### Flow

```text
1 second
↓
Callback Queue
↓
Call Stack
↓
Execute

1 second
↓
Callback Queue
↓
Call Stack
↓
Execute
```

---

## clearTimeout()

Used to cancel a timeout before execution.

### Example

```javascript
const id = setTimeout(() => {
  console.log("Hello");
}, 5000);

clearTimeout(id);
```

Output:

```text
Nothing
```

---

## clearInterval()

Used to stop an interval.

### Example

```javascript
const id = setInterval(() => {
  console.log("Hello");
}, 1000);

clearInterval(id);
```

Output:

```text
Interval Stopped
```

---

# Difference Between setTimeout and setInterval

| Feature     | setTimeout     | setInterval     |
| ----------- | -------------- | --------------- |
| Execution   | Runs Once      | Runs Repeatedly |
| Delay       | After Delay    | Every Delay     |
| Stop Method | clearTimeout   | clearInterval   |
| Use Case    | Delayed Action | Repeated Action |

---

# React Hooks

Hooks allow Functional Components to use React features such as state and lifecycle methods.

---

## useState

Used to manage state.

### Syntax

```javascript
const [state, setState] = useState(initialValue);
```

### Example

```javascript
const [count, setCount] = useState(0);
```

---

## Internal Working of useState

React stores state values internally in a hooks array.

### First Render

```javascript
hooks = [0];
```

```javascript
const [count, setCount] = useState(0);
```

Count becomes:

```javascript
0
```

---

### State Update

```javascript
setCount(1);
```

React stores:

```javascript
hooks = [1];
```

Schedules re-render.

---

### Re-render

React executes component again.

```javascript
count = hooks[0];
```

Count becomes:

```javascript
1
```

UI updates.

---

## Why Hooks Must Be Called in Same Order

React identifies hooks using their position.

Correct:

```javascript
const [name, setName] = useState("");
const [age, setAge] = useState(20);
```

Wrong:

```javascript
if (condition) {
  useState("");
}
```

Hook order changes and React gets confused.

---

## Multiple State Updates

### Problem

```javascript
setCount(count + 1);
setCount(count + 1);
setCount(count + 1);
```

Current count:

```javascript
0
```

React queues:

```javascript
setCount(1);
setCount(1);
setCount(1);
```

Final value:

```javascript
1
```

---

## Functional Update

```javascript
setCount(prev => prev + 1);
setCount(prev => prev + 1);
setCount(prev => prev + 1);
```

Processing:

```text
0 → 1
1 → 2
2 → 3
```

Final value:

```javascript
3
```

---

## useEffect

Used for side effects.

Examples:

* API Calls
* Timers
* Event Listeners
* Subscriptions

### Syntax

```javascript
useEffect(() => {
  // side effect
}, []);
```

---

## API Fetch Example

```javascript
useEffect(() => {
  async function fetchUsers() {
    const response = await fetch(
      "https://api.github.com/users"
    );

    const users = await response.json();
    setUsers(users);
  }

  fetchUsers();
}, []);
```

---

## Why useEffect Cannot Be Async

Wrong:

```javascript
useEffect(async () => {
  const data = await fetch(...);
}, []);
```

React expects:

* Nothing
* Cleanup Function

Async functions return a Promise.

Correct:

```javascript
useEffect(() => {
  async function fetchData() {
    const data = await fetch(...);
  }

  fetchData();
}, []);
```

---

# React Cleanup Function

Used to prevent memory leaks.

### Example

```javascript
useEffect(() => {
  const id = setInterval(() => {
    console.log("Running");
  }, 1000);

  return () => {
    clearInterval(id);
  };
}, []);
```

When component unmounts:

```text
clearInterval(id)
```

runs automatically.

---

# Interview Summary

* JavaScript is single-threaded.
* `setTimeout` runs once after delay.
* `setInterval` runs repeatedly after delay.
* Both use Web APIs, Callback Queue, and Event Loop.
* `useState` stores state and triggers re-renders.
* Functional updates avoid stale state issues.
* `useEffect` handles side effects.
* API calls should be inside `useEffect`.
* Always clean intervals and subscriptions using cleanup functions.

  # JavaScript Timers Quick Notes

## Why use `clearTimeout()` if `setTimeout()` runs only once?

`setTimeout()` schedules a task for the future.

```js
const id = setTimeout(() => {
  console.log("Hello");
}, 5000);
```

Sometimes the task is no longer needed before it executes.

```js
clearTimeout(id);
```

### Common Uses

* Cancel delayed actions
* Debouncing search inputs
* React component cleanup

---

## Difference Between `setTimeout()` and `setInterval()`

### setTimeout

Runs once after a delay.

```js
setTimeout(() => {
  console.log("Hello");
}, 3000);
```

Output:

```text
Hello
```

---

### setInterval

Runs repeatedly after every delay.

```js
setInterval(() => {
  console.log("Hello");
}, 1000);
```

Output:

```text
Hello
Hello
Hello
...
```

---

| Feature   | setTimeout   | setInterval   |
| --------- | ------------ | ------------- |
| Execution | Once         | Repeatedly    |
| Stop      | clearTimeout | clearInterval |
| Use Case  | Delayed Task | Repeated Task |

---

## How `clearTimeout()` and `clearInterval()` Work Internally

When a timer is created:

```js
const id = setTimeout(fn, 5000);
```

Browser stores:

```text
ID = 1
Delay = 5000ms
Callback = fn
```

Calling:

```js
clearTimeout(id);
```

tells the browser:

```text
Find Timer ID 1
Remove it
Do not execute callback
```

Same for intervals:

```js
const id = setInterval(fn, 1000);

clearInterval(id);
```

Browser removes the repeating timer and stops scheduling callbacks.

---

## Interview Answer

* `setTimeout()` runs once after a delay.
* `setInterval()` runs repeatedly after a delay.
* Both return a timer ID.
* `clearTimeout(id)` cancels a scheduled timeout.
* `clearInterval(id)` stops a running interval.
* Browser manages timers using Web APIs, Callback Queue, and the Event Loop.

