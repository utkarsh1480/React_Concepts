UseEffet is a react Hook that  lets you perform side effects in your functional components.
sideEffect : - Fetching data from APIs
- Setting up timers (setInterval, setTimeout)
- Subscribing to external services
- Manually updating the DOM
- Adding event listeners
### Why Do We Need useEffect?

**React components are functions that:**

1. Run
2. Return JSX
3. React updates the screen

This happens **synchronously** - React needs JSX immediately.

**Problem:** What if you need to do something that takes time (like fetching data)?

You can't pause the component function and wait!

**Solution:** useEffect runs your code **AFTER** the component renders.

---

##
React Rendering Model : Component function runs → Returns JSX → async React updates screen

Without UseEffect
```
 async function Fetch(count) {
  const response = await fetch(
    `https://api.github.com/users?per_page=${count}`
  );

  const user = await response.json();
  setUser(user);
 console.log("this is ferch function");
 
}
Fetch(count);
```
### What Happen

```
1. Component renders → fetch runs
2. Fetch completes → setData called
3. State changes → Component re-renders
4. Component renders → fetch runs AGAIN
5. Fetch completes → setData called
6. State changes → Component re-renders
7. Infinite loop! 🔁💥
```

## Basic Syntax and Structure

### useEffect Basic Structure

```jsx
import { useEffect } from 'react';

useEffect(() => {
  // 1. Effect function - Your side effect code here

  return () => {
    // 2. Cleanup function (optional) - Clean up code here
  };
}, [dependencies]); // 3. Dependency array - Controls when effect runs

```

**Three parts:**

1. **Effect Function** - The code you want to run
2. **Cleanup Function** (optional) - Code to clean up (returned from effect)
3. **Dependency Array** - Tells React when to run the effect

### When Does useEffect Run?

**Timeline:**

```
1. Component renders (JSX returned)
2. Screen updates (user sees changes)
3. THEN useEffect runs (after everything)

```

**Key Point:** useEffect runs **AFTER** the render, not during it. This prevents blocking the UI.

### Interview Question: Why not make useEffect async?

❌ Wrong
```
useEffect(async () => {
  const data = await fetch(...);
}, []);

React expects useEffect to return either:

nothing (undefined)
a cleanup function

An async function returns a Promise, which React doesn't expect.
```
### When Does useEffect Run?

**Timeline:**

```
1. Component renders (JSX returned)
2. Screen updates (user sees changes)
3. THEN useEffect runs (after everything)

```

**Key Point:** useEffect runs **AFTER** the render, not during it. This prevents blocking the UI.

## Using fetch with useEffect

### The Correct Way to Fetch Data

```jsx
import { useState, useEffect } from 'react';

function App() {
  const [data, setData] = useState(null);

  useEffect(() => {
    fetch('https://api.example.com/users')
      .then(res => res.json())
      .then(result => setData(result));
  }, []); // ← Empty array = run once

  return <div>{data ? data.name : 'Loading...'}</div>;
}

```

**What happens:**

```
First Render:
1. Component renders with data = null
2. Returns "Loading..."
3. Screen shows "Loading..."
4. AFTER render: useEffect runs
5. Fetch starts (happens in background)

When fetch completes:
6. setData called with result
7. Component re-renders with data
8. useEffect sees [] → "Already ran, skip"
9. Returns data.name
10. Screen shows the data
11. Done! ✅

```
## The empty array [] is KEY - it prevents the infinite loop!
