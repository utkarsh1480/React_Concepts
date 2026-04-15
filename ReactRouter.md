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
### Nested Routing 
```JS
<Routes>
  <Route path="/dashboard" element={<Dashboard />}>
    {/* Children nested inside Dashboard */}
    <Route path="profile" element={<Profile />} />
    <Route path="settings" element={<Settings />} />
  </Route>
</Routes>

```
### Clean, no repetition. But now you need Outlet to tell React Router where to render the child.
```
<Route index element={<DashboardHome />} />
index = default child when no sub-path is matched
```

### Navigate
```
What is it: useNavigate is a hook that lets you navigate programmatically — without the user clicking a Link.
When to use it?
After form submission → redirect to dashboard
After logout → redirect to login
After an API call → redirect based on result
```
```JS
// Go to a route
navigate('/dashboard');

// Go BACK (like browser back button)
navigate(-1);

// Go FORWARD
navigate(1);

// Replace current history (no back button)
navigate('/dashboard', { replace: true });

// Pass state to next page
navigate('/dashboard', { state: { user: 'John' } });
```
### Receiving state on the next page:
```
import { useLocation } from 'react-router-dom';

function Dashboard() {
  const location = useLocation();
  console.log(location.state.user); // "John"
}
```
```
navigate('/path', { state: {} }) → sends data to next page
useLocation().state → receives that data
State is temporary — lost on page refresh
Always use optional chaining ?. when reading state
For persistent data → use localStorage or Context API
What is useLocation Exactly?
useLocation gives you the current URL information as an object:
```
### RealWorld example of the send State 
```JS
/ After placing order
navigate('/order-confirmation', {
  state: {
    orderId: 'ORD123',
    items: cartItems,
    total: 1299
  }
});

// On confirmation page
const { orderId, items, total } = location.state;
```

