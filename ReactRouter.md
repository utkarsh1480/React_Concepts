<Link> Component: Link is the basic navigation component in React Router. It replaces the HTML <a> tag for client-side navigation (no page reload).
  
### How it works internally:

Renders an <a> tag in the DOM
Intercepts the click event and prevents default browser navigation
Uses the History API (pushState) to update the URL
React Router matches the new URL and renders the correct component — no full page reload

<NavLink> Component: NavLink is just like Link but with extra awareness — it knows if its route is currently active.
Auto Active Class: When the route matches, NavLink automatically adds an active class to the element.

### The end Prop (Important!)
Without end, the / route would always be active because every path starts with /.
jsx// ❌ Without end — Home is always active
<NavLink to="/">Home</NavLink>

// ✅ With end — Home only active on exact "/"
<NavLink to="/" end>Home</NavLink>

### Why we need Link if we have <a>
```
When you click a normal <a> tag:

Browser sends a full HTTP request to the server
Server sends back the entire HTML page
Page completely reloads
All React state is lost
All component re-mounts from scratch
```
```
When you click Link:

Click event is intercepted by React Router
No request is sent to the server
URL is updated using History API (pushState)
React Router matches the new URL
Only the matching component swaps out(So "Swaps Out" Simply Means

The old component is removed from the DOM and the new matching component is placed in its position — only inside the <Routes> area.


Navbar → never removed, always visible
Home, About, Contact → only one renders at a time based on URL)
State is preserved, no full reload
```

