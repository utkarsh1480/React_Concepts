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

## Interview Tip

A common React pattern is:
```
useEffect(() => {
  async function fetchData() {
    try {
      ...
    } catch (error) {
      ...
    } finally {
      ...
    }
  }

  fetchData();
}, []);
```
## Because:
```
useEffect itself cannot be async.
try-catch-finally should be inside the async function.
setLoading(false) should run in finally so it executes whether the API succeeds or fails.
```

## Using setInterval with useEffect

### Why Timers Need useEffect

**❌ Without useEffect - Timers Pile Up:**

```jsx
function Counter() {
  const [count, setCount] = useState(0);

  // BAD! Don't do this
  setInterval(() => {
    console.log('Tick');
  }, 1000);

  return (
    <div>
      <p>{count}</p>
      <button onClick={() => setCount(count + 1)}>Click</button>
    </div>
  );
}

```

**What happens:**
First render:
→ Interval-1 created (ticks every second)

You click button:
→ Component re-renders
→ Interval-2 created (ticks every second)
→ Now TWO intervals running!

You click again:
→ Component re-renders
→ Interval-3 created
→ Now THREE intervals running!

Result: Timers keep piling up! Memory leak! 💥

### Correct Way - With useEffect and Cleanup

```jsx
function Clock() {
  const [time, setTime] = useState(new Date().toLocaleTimeString());

  useEffect(() => {
    const intervalId = setInterval(() => {
      setTime(new Date().toLocaleTimeString());
    }, 1000);

    // Cleanup function
    return () => {
      clearInterval(intervalId);
    };
  }, []); // Run once

  return <div>Time: {time}</div>;
}

```
### Common Things That Need Cleanup

### 1. Timers

```jsx
useEffect(() => {
  const id = setInterval(() => {}, 1000);
  return () => clearInterval(id);
}, []);

```

---

### 2. Event Listeners

```jsx
useEffect(() => {
  function handleScroll() {
    console.log('Scrolling...');
  }

  window.addEventListener('scroll', handleScroll);

  return () => {
    window.removeEventListener('scroll', handleScroll);
  };
}, []);

```

---

### 3. Subscriptions

```jsx
useEffect(() => {
  const subscription = dataSource.subscribe();

  return () => {
    subscription.unsubscribe();
  };
```
## Controlled vs Uncontrolled Inputs

### Understanding Input Fields in React

**Question:** "Input fields work without React. Why use state?"

**Answer:** You need state to **control** what's in the input.

---

### Uncontrolled Input (Browser Controls It)

```jsx
function App() {
  return <input type="text" />;
}

```

**Who controls the value?** The **browser** (not React!)

**What happens when you type:**

```
1. You type "hello"
2. Browser updates the input
3. React does nothing

```
It works like plain HTML.
### Problem 1: Can't Transform Input

**Try to make input uppercase:**

```jsx
function App() {
  const handleChange = (e) => {
    console.log(e.target.value); // Can READ it
    // But can't make it uppercase in the input!
  };

  return <input onChange={handleChange} />;
}

```

**Result:** User types "hello" → Input shows "hello" (not "HELLO")

You can read the value but can't control what's displayed.

---

### Problem 2: Can't Access Value Later

```jsx
function App() {
  const handleSubmit = () => {
    // How do you get the input value here? 🤔
    // You'd need refs or DOM queries
  };

  return (
    <>
      <input onChange={(e) => console.log(e.target.value)} />
      <button onClick={handleSubmit}>Submit</button>
    </>
  );
}

```

You can't access the input value in `handleSubmit`!

### Controlled Input (React Controls It)

```jsx
function App() {
  const [name, setName] = useState('');

  return (
    <input
      value={name}  // ← React controls the value
      onChange={(e) => setName(e.target.value)}
    />
  );
}

```

**Who controls the value?** **React!**

**What happens when you type "R":**

```
1. You press "R" key
2. onChange event fires
3. setName('R') called
4. name state becomes 'R'
5. Component re-renders
6. React sets input.value = 'R'
7. Browser displays 'R'

```

**React overwrites the input value on every keystroke!**

### Solution 1: Transform Input

```jsx
function App() {
  const [name, setName] = useState('');

  return (
    <input
      value={name}
      onChange={(e) => setName(e.target.value.toUpperCase())}
    />
  );
}

```

**Result:** User types "hello" → Input shows "HELLO" ✅

---

### Solution 2: Access Value Anywhere

```jsx
function App() {
  const [name, setName] = useState('');

  const handleSubmit = () => {
    console.log('Submitting:', name); // ✅ Easy access!
    fetch('/api/user', {
      method: 'POST',
      body: JSON.stringify({ name })
    });
  };

  return (
    <>
      <input
        value={name}
        onChange={(e) => setName(e.target.value)}
      />
      <button onClick={handleSubmit}>Submit</button>
    </>
  );
}
```
## Solution 3: Reset Input
## Validate While Typing

### The Rule

**To make an input controlled:**

```jsx
<input
  value={stateVariable}           // 1. value prop
  onChange={(e) => setState(...)} // 2. onChange handler
/>

```

**Both are required!**

- Without `value`: Browser controls it (uncontrolled)
- Without `onChange`: Input is frozen (can't type)

### 10. Common Mistakes to Avoid

### Mistake 1: Calling setState During Render (Infinite Loop)

```jsx
// ❌ Wrong
function App() {
  const [count, setCount] = useState(0);

  setCount(count + 1); // Called during render!

  return <div>{count}</div>;
}

```

**Result:** Infinite loop! Component keeps re-rendering.

**Fix:** Use `useEffect` or event handler

```jsx
// ✅ Correct
function App() {
  const [count, setCount] = useState(0);

  useEffect(() => {
    setCount(count + 1);
  }, []); // Or in a button click handler

  return <div>{count}</div>;
}

```
### Mistake 2: Missing Cleanup (Memory Leak)

```jsx
// ❌ Wrong
useEffect(() => {
  setInterval(() => {
    console.log('Tick');
  }, 1000);
  // No cleanup!
}, []);

```

**Problem:** Interval keeps running after component unmounts.

**Fix:** Always cleanup timers

```jsx
// ✅ Correct
useEffect(() => {
  const id = setInterval(() => {
    console.log('Tick');
  }, 1000);

  return () => clearInterval(id);
}, []);

```
### Mistake 3: Making Effect Function async

```jsx
// ❌ Wrong
useEffect(async () => {
  const data = await fetch('/api');
}, []);

```

**Problem:** Can't make effect function async!

**Fix:** Create async function inside

```jsx
// ✅ Correct
useEffect(() => {
  async function fetchData() {
    const data = await fetch('/api');
  }
  fetchData();
}, []);

```
### Key Points to Remember

1. **useEffect runs AFTER render** - Never blocks the UI
2. **Always cleanup timers and listeners** - Prevent memory leaks
3. **Dependencies control when effect runs** - `[]` = once, `[dep]` = when dep changes
4. **Can't make effect async** - Create async function inside instead
5. **Controlled inputs need value + onChange** - Both are required
6. **Empty array `[]` means run once** - Not "no dependencies needed"

---

**End of Notes**

---

## Quick Reference

### When to use useEffect?

- ✅ Fetching data from API
- ✅ Starting/stopping timers
- ✅ Subscribing to services
- ✅ Adding/removing event listeners
- ✅ Updating document title
- ❌ Calculating values from state (just use variables)
- ❌ Handling button clicks (use event handlers)

### Cleanup is needed for:

- ✅ `setInterval` / `setTimeout`
- ✅ Event listeners (`addEventListener`)
- ✅ Subscriptions
- ✅ WebSocket connections
- ❌ Regular `fetch` calls (they clean up automatically)
- ❌ Setting state (doesn't need cleanup)
