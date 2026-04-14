```JS
Context API is React's built-in solution for sharing state/data across components without passing props manually at every level (called "prop drilling").
Interview Questions
Basic
Q1. What is Context API and why do we use it?
For sharing global data across components without prop drilling. Common use cases: auth, theme, language.
Q2. What is prop drilling and how does Context solve it?
Prop drilling = passing props through many intermediate components. Context lets any component access data directly via useContext.
Q3. How do you create and use a Context?
createContext() → wrap with <Provider value={...}> → consume with useContext(YourContext).
Intermediate
Q4. What happens when Context value changes?
All components consuming that context re-render. This can be a performance issue if the value changes frequently.
Q5. How do you optimize Context to avoid unnecessary re-renders?
Split contexts (don't put unrelated state together), memoize the value with useMemo, or use React.memo on child components.
jsxconst value = useMemo(() => ({ user, setUser }), [user]);
Q6. Can you have multiple Contexts in one app?
Yes! Best practice is to split them by concern — AuthContext, ThemeContext, CartContext separately.
Q7. What's the difference between Context API and Redux?
Context APIReduxBuilt into ReactExternal librarySimple global stateComplex state with actions/reducersNo DevTools (by default)Powerful DevToolsCan cause re-rendersMore optimized with selectors
Advanced
Q8. How would you implement a Context with reducer (like Redux pattern)?
Combine useContext + useReducer — dispatch actions to update state, components consume state from context.
Q9. Why might Context API be a bad choice for high-frequency updates?
Every context value change causes all consumers to re-render. For state like mouse position updating 60fps, this kills performance. Use Zustand or local state instead.
Q10. How do you handle async operations (like API calls) in Context?
Create async functions inside the provider and expose them in the value. Use useEffect for initial data fetching.
```



```



<img width="1440" height="1102" alt="image" src="https://github.com/user-attachments/assets/aacd8eec-ff36-4856-a909-ccfb98bfc5af" />

Yes, your statement is completely correct! Here's the key rule:

Any component inside the <Context.Provider> wrapper — at any depth — can access the context data.
```
```JS
### How to Implement Contect API

### Method 01

```
