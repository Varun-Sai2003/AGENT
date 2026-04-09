---
skill: react
version_covered: React 19.2 (with React 18 compatibility notes)
depth: comprehensive
last_updated: 2026-03-21
source: web-research
use_when: >
  Load this skill when building web user interfaces with React, creating
  single-page applications, working with component-based architectures,
  using React hooks, implementing Server Components, using the Activity
  component, optimizing rendering performance, or integrating with the
  React ecosystem (Next.js, Vite, etc.).
---

# React — Expert Reference

> **Quick Summary**: React is a declarative, component-based JavaScript library for building user interfaces. React 19 introduces Server Components, new hooks (`useActionState`, `useFormStatus`, `useOptimistic`, `use`), native document metadata support, and enhanced concurrent rendering. React 19.2 adds the `<Activity />` component for state-preserving visibility toggling and `cacheSignal` for server-side resource management. The React Compiler (v1.0 stable, Oct 2025) automates memoization — all designed to make apps faster, simpler, and more maintainable.

---

## 1. Overview

### What is React?

React is an open-source JavaScript library maintained by Meta for building **declarative, component-based user interfaces**. It uses a Virtual DOM for efficient updates and a one-way data flow model that makes state changes predictable and debuggable.

### Why React?

- **Declarative UI**: Describe _what_ the UI should look like, not _how_ to manipulate the DOM
- **Component model**: Build encapsulated, reusable pieces that compose into complex UIs
- **Massive ecosystem**: Thousands of libraries, frameworks (Next.js, Remix, Vite), and tools
- **Industry standard**: Used by Meta, Netflix, Airbnb, Discord, and millions of applications
- **Cross-platform**: Powers web (React DOM), mobile (React Native), desktop, and VR

### When to Use React vs. Alternatives

| Scenario          | React                  | Vue       | Svelte         | Angular                |
| ----------------- | ---------------------- | --------- | -------------- | ---------------------- |
| Large-scale SPA   | ✅ Best                | ✅ Good   | ⚠️ Growing     | ✅ Best                |
| Rapid prototyping | ✅ Good                | ✅ Best   | ✅ Best        | ❌ Heavy               |
| SEO-critical site | ✅ (Next.js)           | ✅ (Nuxt) | ✅ (SvelteKit) | ✅ (Angular Universal) |
| Tiny widget/embed | ⚠️ Overkill            | ⚠️ OK     | ✅ Best        | ❌ Heavy               |
| Mobile + Web      | ✅ Best (React Native) | ❌        | ❌             | ⚠️ (Ionic)             |

---

## 2. Core Concepts

### The Component Model

Everything in React is a **component** — a JavaScript function that returns JSX (a syntax extension that looks like HTML). Components receive data via **props** and manage internal data via **state**.

```jsx
// A simple functional component
function Greeting({ name }) {
  return <h1>Hello, {name}!</h1>;
}
```

### JSX

JSX compiles to `React.createElement()` calls. In React 19, you don't need to import React in every file (new JSX transform).

```jsx
// JSX
const element = <div className="card">{title}</div>;

// Compiles to
const element = React.createElement("div", { className: "card" }, title);
```

**JSX Rules:**

- Return a single root element (use `<>...</>` fragments to avoid extra DOM nodes)
- Use `className` instead of `class`
- Use `htmlFor` instead of `for`
- All tags must be closed (`<img />`, `<br />`)
- JavaScript expressions go inside `{curly braces}`

### One-Way Data Flow

Data flows **down** from parent to child via props. Children communicate **up** via callback functions passed as props. This makes data flow predictable and easy to debug.

### The Virtual DOM

React maintains a lightweight in-memory representation of the actual DOM. When state changes, React:

1. Creates a new Virtual DOM tree
2. Diffs it against the previous tree (reconciliation)
3. Batches and applies only the minimal set of actual DOM changes

### Rendering Lifecycle

1. **Trigger**: State change, prop change, or parent re-render
2. **Render**: React calls your component function to get the new JSX
3. **Commit**: React updates the DOM with the differences

### Key Terms

| Term                     | Definition                                                              |
| ------------------------ | ----------------------------------------------------------------------- |
| **Component**            | A reusable function that returns JSX describing UI                      |
| **Props**                | Read-only data passed from parent to child                              |
| **State**                | Mutable data managed within a component that triggers re-renders        |
| **Hook**                 | A function starting with `use` that lets you "hook into" React features |
| **JSX**                  | Syntax extension that lets you write HTML-like code in JavaScript       |
| **Virtual DOM**          | In-memory representation of the real DOM for efficient updates          |
| **Reconciliation**       | The algorithm React uses to diff and update the DOM                     |
| **Fiber**                | React's internal reconciliation engine enabling concurrent rendering    |
| **Server Component**     | A component that renders exclusively on the server                      |
| **Client Component**     | A component that renders on the client (marked with `"use client"`)     |
| **Suspense**             | A mechanism for declaratively handling async loading states             |
| **Concurrent Rendering** | React's ability to prepare multiple UI versions simultaneously          |
| **Activity**             | A React 19.2 primitive for state-preserving visibility toggling         |
| **React Compiler**       | Build-time tool that auto-memoizes components, hooks, and values        |

---

## 3. Syntax / API / Command Reference

### Component Types

```jsx
// ✅ Functional Component (standard in React 19)
function Button({ onClick, children }) {
  return <button onClick={onClick}>{children}</button>;
}

// ✅ Arrow function variant
const Button = ({ onClick, children }) => (
  <button onClick={onClick}>{children}</button>
);

// ❌ Class Component (legacy — avoid in new code)
class Button extends React.Component {
  render() {
    return <button onClick={this.props.onClick}>{this.props.children}</button>;
  }
}
```

### All React 19 Hooks

#### State Hooks

```jsx
// useState — simple state
const [count, setCount] = useState(0);
setCount((prev) => prev + 1); // Always use updater function for derived state

// useReducer — complex state logic
const [state, dispatch] = useReducer(reducer, initialState);
dispatch({ type: "INCREMENT" });

function reducer(state, action) {
  switch (action.type) {
    case "INCREMENT":
      return { ...state, count: state.count + 1 };
    case "DECREMENT":
      return { ...state, count: state.count - 1 };
    default:
      throw new Error(`Unknown action: ${action.type}`);
  }
}
```

#### Effect Hooks

```jsx
// useEffect — side effects (data fetching, subscriptions, DOM manipulation)
useEffect(() => {
  const subscription = api.subscribe((data) => setData(data));
  return () => subscription.unsubscribe(); // Cleanup!
}, [dependency]); // Runs when `dependency` changes

// useLayoutEffect — synchronous DOM measurement (runs before paint)
useLayoutEffect(() => {
  const { height } = ref.current.getBoundingClientRect();
  setHeight(height);
}, []);

// useInsertionEffect — for CSS-in-JS libraries (runs before DOM mutations)
useInsertionEffect(() => {
  const style = document.createElement("style");
  style.textContent = css;
  document.head.appendChild(style);
  return () => style.remove();
}, [css]);
```

#### Ref Hooks

```jsx
// useRef — persist mutable value across renders without re-rendering
const inputRef = useRef(null);
const renderCount = useRef(0);

// Access DOM element
<input ref={inputRef} />;
inputRef.current.focus();

// useImperativeHandle — customize ref exposed to parent
useImperativeHandle(
  ref,
  () => ({
    focus: () => inputRef.current.focus(),
    scrollIntoView: () => inputRef.current.scrollIntoView(),
  }),
  [],
);
```

#### Context Hooks

```jsx
// createContext + useContext
const ThemeContext = createContext("light");

function App() {
  return (
    <ThemeContext.Provider value="dark">
      <Toolbar />
    </ThemeContext.Provider>
  );
}

function ThemedButton() {
  const theme = useContext(ThemeContext); // 'dark'
  return <button className={theme}>Click me</button>;
}
```

#### Performance Hooks

```jsx
// useMemo — memoize expensive computation
const sortedItems = useMemo(
  () => items.sort((a, b) => a.name.localeCompare(b.name)),
  [items],
);

// useCallback — memoize function reference
const handleClick = useCallback((id) => {
  setItems((prev) => prev.filter((item) => item.id !== id));
}, []);

// Note: React Compiler in React 19 auto-memoizes, reducing need for these!
```

#### Transition Hooks

```jsx
// useTransition — mark non-urgent state updates
const [isPending, startTransition] = useTransition();

function handleSearch(query) {
  startTransition(() => {
    setSearchResults(filterResults(query)); // Low priority
  });
}

// useDeferredValue — defer re-rendering of a value
const deferredQuery = useDeferredValue(query);
// UI stays responsive; deferredQuery updates later
```

#### React 19 New Hooks

```jsx
// useActionState — manage form action state
const [state, formAction, isPending] = useActionState(
  async (prevState, formData) => {
    const result = await submitForm(formData);
    return result;
  },
  { error: null, data: null } // initial state
);

<form action={formAction}>
  <input name="email" />
  <button disabled={isPending}>
    {isPending ? 'Submitting...' : 'Submit'}
  </button>
  {state.error && <p className="error">{state.error}</p>}
</form>

// useFormStatus — get parent form submission status (child component)
function SubmitButton() {
  const { pending, data, method, action } = useFormStatus();
  return <button disabled={pending}>{pending ? 'Saving...' : 'Save'}</button>;
}

// useOptimistic — optimistic UI updates
const [optimisticMessages, addOptimistic] = useOptimistic(
  messages,
  (currentMessages, newMessage) => [
    ...currentMessages,
    { ...newMessage, sending: true }
  ]
);

async function sendMessage(formData) {
  const message = formData.get('message');
  addOptimistic({ text: message, id: Date.now() });
  await api.send(message);
}

// use() — read resources (promises, context) during render
function UserProfile({ userPromise }) {
  const user = use(userPromise); // Suspends until resolved
  return <h1>{user.name}</h1>;
}

// useSyncExternalStore — subscribe to external stores
const snapshot = useSyncExternalStore(
  store.subscribe,
  store.getSnapshot,
  store.getServerSnapshot // for SSR
);

// useId — generate unique IDs for accessibility
const id = useId();
<label htmlFor={id}>Email</label>
<input id={id} type="email" />
```

### Server Components (React 19)

```jsx
// Server Component (default — no directive needed)
// Can access databases, file system, etc. directly
async function ProductList() {
  const products = await db.query("SELECT * FROM products");
  return (
    <ul>
      {products.map((p) => (
        <ProductCard key={p.id} product={p} />
      ))}
    </ul>
  );
}

// Client Component — must be marked explicitly
("use client");
import { useState } from "react";

function AddToCartButton({ productId }) {
  const [added, setAdded] = useState(false);
  return (
    <button onClick={() => setAdded(true)}>
      {added ? "✓ Added" : "Add to Cart"}
    </button>
  );
}
```

**Server vs Client Component Rules:**
| Feature | Server Component | Client Component |
|---|---|---|
| Directive | None (default) | `'use client'` |
| State/Hooks | ❌ No | ✅ Yes |
| Event handlers | ❌ No | ✅ Yes |
| Browser APIs | ❌ No | ✅ Yes |
| Direct DB/FS access | ✅ Yes | ❌ No |
| Async component | ✅ Yes | ❌ No |
| Reduces bundle size | ✅ Yes | ❌ Adds to bundle |

### Server Actions (React 19)

```jsx
// Define a server action
"use server";

async function createUser(formData) {
  const name = formData.get("name");
  const email = formData.get("email");
  await db.insert("users", { name, email });
  revalidatePath("/users");
}

// Use in a Client Component
("use client");

function SignupForm() {
  return (
    <form action={createUser}>
      <input name="name" required />
      <input name="email" type="email" required />
      <button type="submit">Sign Up</button>
    </form>
  );
}
```

### Suspense & Error Boundaries

```jsx
import { Suspense } from "react";

function App() {
  return (
    <ErrorBoundary fallback={<p>Something went wrong.</p>}>
      <Suspense fallback={<Skeleton />}>
        <UserProfile />
      </Suspense>
    </ErrorBoundary>
  );
}

// Error Boundary (still requires class component)
class ErrorBoundary extends React.Component {
  state = { hasError: false };

  static getDerivedStateFromError(error) {
    return { hasError: true };
  }

  componentDidCatch(error, info) {
    logErrorToService(error, info.componentStack);
  }

  render() {
    if (this.state.hasError) return this.props.fallback;
    return this.props.children;
  }
}
```

### Portals

```jsx
import { createPortal } from "react-dom";

function Modal({ children, onClose }) {
  return createPortal(
    <div className="modal-overlay" onClick={onClose}>
      <div className="modal-content" onClick={(e) => e.stopPropagation()}>
        {children}
      </div>
    </div>,
    document.getElementById("modal-root"),
  );
}
```

### Document Metadata (React 19)

```jsx
// React 19 hoists these to <head> automatically
function BlogPost({ post }) {
  return (
    <article>
      <title>{post.title} | My Blog</title>
      <meta name="description" content={post.excerpt} />
      <meta name="author" content={post.author} />
      <h1>{post.title}</h1>
      <p>{post.content}</p>
    </article>
  );
}
```

---

## 4. Patterns & Best Practices

### ✅ Do

- **Use functional components with hooks** — they're simpler, more composable, and the future
- **Keep components small and focused** — aim for single responsibility; split at ~200 lines
- **Extract custom hooks** for shared logic (`useLocalStorage`, `useFetch`, `useDebounce`)
- **Organize by feature**: `features/auth/`, `features/dashboard/`, not `components/`, `hooks/`
- **Colocate related files**: component, styles, tests, and types in the same folder
- **Use TypeScript** for type safety, better refactoring, and IDE support
- **Derive state when possible** — compute values during render instead of storing in state
- **Use `key` prop correctly** — stable, unique identifiers (IDs), never array indices for dynamic lists
- **Lift state only as high as needed** — keep state close to where it's used
- **Use Suspense for loading states** — declarative, composable, and framework-aligned
- **Default to Server Components** (in Next.js/frameworks) — add `'use client'` only when needed
- **Trust the React Compiler** — write straightforward code; avoid manual `useMemo`/`useCallback` unless profiling shows need
- **Use `startTransition`** for non-urgent updates (search filtering, tab switching)
- **Prefer composition over inheritance** — use `children` prop for flexible layouts
- **Handle errors with Error Boundaries** — prevent entire app crashes

### ❌ Don't

- **Don't mutate state directly** — always create new objects/arrays

  ```jsx
  // ❌ BAD
  state.items.push(newItem);
  setState(state);

  // ✅ GOOD
  setState((prev) => ({ ...prev, items: [...prev.items, newItem] }));
  ```

- **Don't define components inside components** — creates new component on every render

  ```jsx
  // ❌ BAD — Inner is recreated every render, causing unmount/remount
  function Outer() {
    function Inner() {
      return <div>Hi</div>;
    }
    return <Inner />;
  }

  // ✅ GOOD — Define at module level
  function Inner() {
    return <div>Hi</div>;
  }
  function Outer() {
    return <Inner />;
  }
  ```

- **Don't use `useEffect` for things computable during render**

  ```jsx
  // ❌ BAD — unnecessary effect
  const [fullName, setFullName] = useState("");
  useEffect(() => {
    setFullName(`${firstName} ${lastName}`);
  }, [firstName, lastName]);

  // ✅ GOOD — derive during render
  const fullName = `${firstName} ${lastName}`;
  ```

- **Don't ignore cleanup in useEffect** — subscriptions, timers, event listeners will leak
- **Don't prop-drill excessively** — use Context, Zustand, or Jotai for deep data passing
- **Don't use array index as key** for lists that can reorder, add, or remove items
- **Don't mix controlled and uncontrolled inputs** — pick one approach per input
- **Don't over-optimize prematurely** — profile first, then optimize
- **Don't overuse `useEffect`** — it's for side effects, not for deriving state or business logic
- **Don't ignore accessibility** — always include alt text, keyboard navigation, and ARIA attributes
- **Don't ship everything to the client** — use Server Components to reduce bundle size

### Design Patterns

#### Container/Presentational Pattern

```jsx
// Container — handles data and logic
function UserListContainer() {
  const { data, isLoading } = useFetch("/api/users");
  if (isLoading) return <Skeleton />;
  return <UserList users={data} />;
}

// Presentational — pure rendering
function UserList({ users }) {
  return (
    <ul>
      {users.map((user) => (
        <UserCard key={user.id} user={user} />
      ))}
    </ul>
  );
}
```

#### Compound Components Pattern

```jsx
function Tabs({ children, defaultTab }) {
  const [activeTab, setActiveTab] = useState(defaultTab);
  return (
    <TabsContext.Provider value={{ activeTab, setActiveTab }}>
      <div className="tabs">{children}</div>
    </TabsContext.Provider>
  );
}

Tabs.List = function TabList({ children }) {
  return (
    <div className="tab-list" role="tablist">
      {children}
    </div>
  );
};

Tabs.Tab = function Tab({ value, children }) {
  const { activeTab, setActiveTab } = useContext(TabsContext);
  return (
    <button
      role="tab"
      aria-selected={activeTab === value}
      onClick={() => setActiveTab(value)}
    >
      {children}
    </button>
  );
};

Tabs.Panel = function TabPanel({ value, children }) {
  const { activeTab } = useContext(TabsContext);
  if (activeTab !== value) return null;
  return <div role="tabpanel">{children}</div>;
};

// Usage
<Tabs defaultTab="profile">
  <Tabs.List>
    <Tabs.Tab value="profile">Profile</Tabs.Tab>
    <Tabs.Tab value="settings">Settings</Tabs.Tab>
  </Tabs.List>
  <Tabs.Panel value="profile">
    <ProfileContent />
  </Tabs.Panel>
  <Tabs.Panel value="settings">
    <SettingsContent />
  </Tabs.Panel>
</Tabs>;
```

#### Custom Hook Pattern

```jsx
function useLocalStorage(key, initialValue) {
  const [value, setValue] = useState(() => {
    try {
      const item = localStorage.getItem(key);
      return item ? JSON.parse(item) : initialValue;
    } catch {
      return initialValue;
    }
  });

  useEffect(() => {
    localStorage.setItem(key, JSON.stringify(value));
  }, [key, value]);

  return [value, setValue];
}

// Usage
const [theme, setTheme] = useLocalStorage("theme", "dark");
```

---

## 5. Common Pitfalls & Gotchas

### 1. Stale Closures in useEffect

```jsx
// ❌ BUG — `count` is always 0 inside the interval
useEffect(() => {
  const id = setInterval(() => {
    setCount(count + 1); // Stale closure!
  }, 1000);
  return () => clearInterval(id);
}, []);

// ✅ FIX — use updater function
useEffect(() => {
  const id = setInterval(() => {
    setCount((prev) => prev + 1); // Always fresh
  }, 1000);
  return () => clearInterval(id);
}, []);
```

### 2. Infinite Loop with useEffect

```jsx
// ❌ BUG — creates new object every render → infinite loop
useEffect(() => {
  fetchData(options);
}, [options]); // `options` is a new object every render!

// ✅ FIX — memoize or use primitive deps
const options = useMemo(() => ({ page, limit }), [page, limit]);
useEffect(() => {
  fetchData(options);
}, [options]);
```

### 3. setState Is Asynchronous

```jsx
// ❌ BUG — count won't be 3
function handleTripleClick() {
  setCount(count + 1);
  setCount(count + 1);
  setCount(count + 1); // All use same stale `count`
}

// ✅ FIX — use updater function
function handleTripleClick() {
  setCount((prev) => prev + 1);
  setCount((prev) => prev + 1);
  setCount((prev) => prev + 1); // Correctly increments by 3
}
```

### 4. Object/Array State Mutations

```jsx
// ❌ BUG — mutating existing array, React won't re-render
const handleAdd = () => {
  items.push(newItem);
  setItems(items); // Same reference!
};

// ✅ FIX — create new array
const handleAdd = () => {
  setItems([...items, newItem]);
};

// For nested objects:
setUser((prev) => ({
  ...prev,
  address: { ...prev.address, city: "New York" },
}));
```

### 5. Missing Dependency Array in useEffect

```jsx
// ❌ BUG — runs on EVERY render (no deps)
useEffect(() => {
  fetchData();
}); // Missing dependency array!

// ✅ Runs once on mount
useEffect(() => {
  fetchData();
}, []); // Empty array = mount only

// ✅ Runs when `id` changes
useEffect(() => {
  fetchData(id);
}, [id]);
```

### 6. Rendering Issues with Conditional Hooks

```jsx
// ❌ RULE VIOLATION — hooks must be called in same order every render
if (isLoggedIn) {
  const [user, setUser] = useState(null); // Conditional hook!
}

// ✅ FIX — always call, conditionally use
const [user, setUser] = useState(null);
// Use `user` only when `isLoggedIn` is true
```

### 7. Memory Leaks

```jsx
// ❌ BUG — no cleanup
useEffect(() => {
  window.addEventListener("resize", handleResize);
}, []);

// ✅ FIX — cleanup on unmount
useEffect(() => {
  window.addEventListener("resize", handleResize);
  return () => window.removeEventListener("resize", handleResize);
}, []);
```

---

## 6. Advanced Topics

### React Compiler (Stable v1.0 — October 2025)

The React Compiler reached **stable v1.0** at React Conf (October 2025) and is now production-ready. It's a build-time Babel plugin that performs static analysis and automatically inserts granular memoization — eliminating the need for manual `useMemo`, `useCallback`, and `React.memo` in most cases. It's been battle-tested at Meta and is integrated with Next.js, Vite, and Expo.

```jsx
// Before: manual memoization
const MemoizedList = React.memo(({ items }) => {
  const sorted = useMemo(() => items.sort(compareFn), [items]);
  const handleClick = useCallback((id) => selectItem(id), []);
  return sorted.map((item) => <Item key={item.id} onClick={handleClick} />);
});

// After (React Compiler): just write normal code
function List({ items }) {
  const sorted = items.sort(compareFn);
  const handleClick = (id) => selectItem(id);
  return sorted.map((item) => <Item key={item.id} onClick={handleClick} />);
}
// The compiler handles memoization automatically!

// Installation (Vite example)
// npm install babel-plugin-react-compiler
// vite.config.js: plugins: [reactCompiler()]
```

> **Note**: `useMemo` and `useCallback` still serve as escape hatches for edge cases where precise control over effect dependencies is needed.

### Activity Component (React 19.2)

The `<Activity />` component is a new primitive for **state-preserving visibility toggling**. Unlike conditional rendering (which unmounts/remounts), Activity visually hides children while preserving their React state and DOM.

```jsx
import { Activity } from "react";

function TabbedLayout({ activeTab }) {
  return (
    <div>
      <Activity mode={activeTab === "home" ? "visible" : "hidden"}>
        <HomePage />
      </Activity>
      <Activity mode={activeTab === "settings" ? "visible" : "hidden"}>
        <SettingsPage />
      </Activity>
    </div>
  );
}
// When hidden:
// - CSS display: none applied
// - Effects are destroyed (subscriptions cleaned up)
// - Updates are deferred until idle
// When visible again:
// - State is restored instantly
// - Effects are recreated
```

**Use cases**: Tabbed interfaces, modals, sidebars, off-screen pre-rendering.

### cacheSignal (React 19.2 — Server Components)

`cacheSignal` provides an `AbortSignal` for canceling in-flight operations in Server Components when the cached result is no longer needed.

```jsx
// Server Component
async function SearchResults({ query }) {
  const signal = cacheSignal();
  const results = await fetch(`/api/search?q=${query}`, { signal });
  // If React drops this render (user navigated away),
  // the signal aborts, canceling the fetch
  return <ResultsList data={await results.json()} />;
}
```

### Concurrent Rendering

React 19 enables concurrent rendering by default. React can:

- **Interrupt** rendering to handle high-priority updates
- **Prepare** multiple versions of the UI simultaneously
- **Transition** between states without blocking

```jsx
function SearchPage() {
  const [query, setQuery] = useState("");
  const [isPending, startTransition] = useTransition();

  function handleChange(e) {
    // Urgent: update input immediately
    setQuery(e.target.value);

    // Non-urgent: filter results in background
    startTransition(() => {
      setFilteredResults(filterData(e.target.value));
    });
  }

  return (
    <>
      <input value={query} onChange={handleChange} />
      {isPending ? <Spinner /> : <Results data={filteredResults} />}
    </>
  );
}
```

### Code Splitting & Lazy Loading

```jsx
import { lazy, Suspense } from "react";

// Lazy load heavy components
const Dashboard = lazy(() => import("./Dashboard"));
const Analytics = lazy(() => import("./Analytics"));

function App() {
  return (
    <Suspense fallback={<PageSkeleton />}>
      <Routes>
        <Route path="/dashboard" element={<Dashboard />} />
        <Route path="/analytics" element={<Analytics />} />
      </Routes>
    </Suspense>
  );
}
```

### Performance Optimization Strategies

1. **Virtualize long lists** — use `react-window` or `@tanstack/react-virtual`
2. **Debounce expensive operations** — search inputs, API calls
3. **Use `startTransition`** for non-critical updates
4. **Split code** with `lazy()` and `Suspense`
5. **Profile with React DevTools Profiler** — identify slow renders
6. **Use Server Components** to reduce client bundle size

### Strict Mode

```jsx
// Wrap app in StrictMode for development checks
import { StrictMode } from "react";
import { createRoot } from "react-dom/client";

createRoot(document.getElementById("root")).render(
  <StrictMode>
    <App />
  </StrictMode>,
);
```

StrictMode in development:

- Double-invokes render and effects to detect impure renders
- Warns about deprecated APIs
- Helps find common bugs before production

---

## 7. Ecosystem & Tooling

| Tool/Library              | Purpose                                  | When to Use                                          |
| ------------------------- | ---------------------------------------- | ---------------------------------------------------- |
| **Next.js 15**            | Full-stack React framework (SSR/SSG/ISR) | SEO-critical apps, full-stack apps, production sites |
| **Vite 6**                | Fast build tool & dev server             | New SPAs, when you need speed and simplicity         |
| **Remix**                 | Full-stack framework (loaders/actions)   | Data-heavy apps, progressive enhancement             |
| **Redux Toolkit**         | Predictable state management             | Large apps with complex global state                 |
| **Zustand 5.x**           | Lightweight state management             | Simple global state, fast prototyping                |
| **Jotai 2.18.x**          | Atomic state management                  | Fine-grained reactivity, minimal boilerplate         |
| **TanStack Query 5**      | Server state / data fetching             | API caching, real-time data, pagination              |
| **React Router 7**        | Client-side routing                      | SPAs needing URL-based navigation                    |
| **TanStack Router 1.x**   | Type-safe routing                        | TypeScript-heavy apps, advanced preloading           |
| **React Hook Form**       | Form management                          | Complex forms with validation                        |
| **Zod**                   | Schema validation                        | Input validation, API contract enforcement           |
| **Framer Motion**         | Animations                               | Smooth UI animations, gesture-based interactions     |
| **Tailwind CSS 4**        | Utility-first CSS                        | Rapid styling, consistent design systems             |
| **Radix UI / shadcn/ui**  | Accessible component primitives          | Building custom design systems                       |
| **React Testing Library** | Component testing                        | Unit/integration tests                               |
| **Playwright / Cypress**  | E2E testing                              | Full user flow testing                               |
| **Storybook 8**           | Component development                    | Isolated component building, documentation           |
| **React Compiler**        | Automatic memoization (build-time)       | All projects — eliminates manual memo code           |

### Project Setup (2026)

```bash
# Vite (recommended for new SPAs)
npm create vite@latest my-app -- --template react-ts
cd my-app && npm install

# Next.js (recommended for full-stack / SSR)
npx create-next-app@latest my-app --typescript --app --src-dir
cd my-app && npm run dev

# Enable React Compiler (Vite)
npm install babel-plugin-react-compiler
# Add to vite.config.ts: plugins: [reactCompiler()]

# Expo (for React Native — see react-native.md)
npx create-expo-app my-app
```

---

## 8. Quick Reference Cheat Sheet

### Hooks at a Glance

| Hook                        | Purpose               | Returns                        |
| --------------------------- | --------------------- | ------------------------------ |
| `useState(init)`            | Local state           | `[value, setValue]`            |
| `useReducer(fn, init)`      | Complex state logic   | `[state, dispatch]`            |
| `useEffect(fn, deps)`       | Side effects          | `void`                         |
| `useLayoutEffect(fn, deps)` | Sync DOM measurement  | `void`                         |
| `useRef(init)`              | Persist mutable value | `{ current: value }`           |
| `useContext(Ctx)`           | Read context value    | `contextValue`                 |
| `useMemo(fn, deps)`         | Memoize computation   | `computedValue`                |
| `useCallback(fn, deps)`     | Memoize function      | `memoizedFn`                   |
| `useTransition()`           | Non-urgent updates    | `[isPending, startTransition]` |
| `useDeferredValue(val)`     | Defer value update    | `deferredValue`                |
| `useId()`                   | Unique ID for a11y    | `string`                       |
| `useActionState(fn, init)`  | Form action state     | `[state, action, isPending]`   |
| `useFormStatus()`           | Parent form status    | `{ pending, data, method }`    |
| `useOptimistic(state, fn)`  | Optimistic updates    | `[optimistic, addOptimistic]`  |
| `use(resource)`             | Read promise/context  | `resolvedValue`                |

### Event Handling Cheat Sheet

```jsx
onClick = { handleClick }; // Click
onChange = { handleChange }; // Input change
onSubmit = { handleSubmit }; // Form submit
onKeyDown = { handleKeyDown }; // Key press
onFocus / onBlur; // Focus events
onMouseEnter / onMouseLeave; // Hover events
```

### Conditional Rendering

```jsx
{
  isLoggedIn && <Dashboard />;
} // Short-circuit
{
  isAdmin ? <Admin /> : <User />;
} // Ternary
{
  status === "loading" && <Spinner />;
} // Status-based
```

---

## 9. Worked Examples

### Example 1: Todo App with useReducer

```jsx
import { useReducer, useState } from "react";

const initialState = { todos: [], filter: "all" };

function todoReducer(state, action) {
  switch (action.type) {
    case "ADD":
      return {
        ...state,
        todos: [
          ...state.todos,
          {
            id: Date.now(),
            text: action.text,
            completed: false,
          },
        ],
      };
    case "TOGGLE":
      return {
        ...state,
        todos: state.todos.map((todo) =>
          todo.id === action.id
            ? { ...todo, completed: !todo.completed }
            : todo,
        ),
      };
    case "DELETE":
      return {
        ...state,
        todos: state.todos.filter((todo) => todo.id !== action.id),
      };
    case "SET_FILTER":
      return { ...state, filter: action.filter };
    default:
      throw new Error(`Unknown action: ${action.type}`);
  }
}

function TodoApp() {
  const [state, dispatch] = useReducer(todoReducer, initialState);
  const [input, setInput] = useState("");

  const filteredTodos = state.todos.filter((todo) => {
    if (state.filter === "active") return !todo.completed;
    if (state.filter === "completed") return todo.completed;
    return true;
  });

  const handleSubmit = (e) => {
    e.preventDefault();
    if (!input.trim()) return;
    dispatch({ type: "ADD", text: input });
    setInput("");
  };

  return (
    <div>
      <h1>Todos ({filteredTodos.length})</h1>
      <form onSubmit={handleSubmit}>
        <input
          value={input}
          onChange={(e) => setInput(e.target.value)}
          placeholder="Add a todo..."
        />
        <button type="submit">Add</button>
      </form>

      <div>
        {["all", "active", "completed"].map((filter) => (
          <button
            key={filter}
            onClick={() => dispatch({ type: "SET_FILTER", filter })}
            style={{ fontWeight: state.filter === filter ? "bold" : "normal" }}
          >
            {filter}
          </button>
        ))}
      </div>

      <ul>
        {filteredTodos.map((todo) => (
          <li key={todo.id}>
            <label>
              <input
                type="checkbox"
                checked={todo.completed}
                onChange={() => dispatch({ type: "TOGGLE", id: todo.id })}
              />
              <span
                style={{
                  textDecoration: todo.completed ? "line-through" : "none",
                }}
              >
                {todo.text}
              </span>
            </label>
            <button onClick={() => dispatch({ type: "DELETE", id: todo.id })}>
              ✕
            </button>
          </li>
        ))}
      </ul>
    </div>
  );
}

export default TodoApp;
```

### Example 2: Data Fetching with Custom Hook + Suspense

```jsx
// useFetch.js — Custom hook for data fetching
import { useState, useEffect } from "react";

function useFetch(url) {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    const controller = new AbortController();

    async function fetchData() {
      try {
        setLoading(true);
        setError(null);
        const response = await fetch(url, { signal: controller.signal });
        if (!response.ok) throw new Error(`HTTP ${response.status}`);
        const json = await response.json();
        setData(json);
      } catch (err) {
        if (err.name !== "AbortError") {
          setError(err.message);
        }
      } finally {
        setLoading(false);
      }
    }

    fetchData();
    return () => controller.abort(); // Cleanup: cancel on unmount/re-fetch
  }, [url]);

  return { data, loading, error };
}

// UserList.jsx — Using the hook
function UserList() {
  const { data: users, loading, error } = useFetch("/api/users");

  if (loading) return <div className="skeleton" />;
  if (error) return <div className="error">Error: {error}</div>;

  return (
    <ul>
      {users.map((user) => (
        <li key={user.id}>
          <strong>{user.name}</strong> — {user.email}
        </li>
      ))}
    </ul>
  );
}
```

### Example 3: Theme Provider with Context

```jsx
import { createContext, useContext, useState, useEffect } from "react";

const ThemeContext = createContext(undefined);

export function ThemeProvider({ children }) {
  const [theme, setTheme] = useState(() => {
    if (typeof window !== "undefined") {
      return localStorage.getItem("theme") || "system";
    }
    return "system";
  });

  const resolvedTheme =
    theme === "system"
      ? window.matchMedia("(prefers-color-scheme: dark)").matches
        ? "dark"
        : "light"
      : theme;

  useEffect(() => {
    document.documentElement.setAttribute("data-theme", resolvedTheme);
    localStorage.setItem("theme", theme);
  }, [theme, resolvedTheme]);

  const toggleTheme = () => {
    setTheme((prev) => (prev === "dark" ? "light" : "dark"));
  };

  return (
    <ThemeContext.Provider
      value={{ theme, resolvedTheme, setTheme, toggleTheme }}
    >
      {children}
    </ThemeContext.Provider>
  );
}

export function useTheme() {
  const context = useContext(ThemeContext);
  if (!context) throw new Error("useTheme must be used within ThemeProvider");
  return context;
}

// Usage
function Header() {
  const { resolvedTheme, toggleTheme } = useTheme();
  return (
    <header>
      <button onClick={toggleTheme}>
        {resolvedTheme === "dark" ? "☀️" : "🌙"}
      </button>
    </header>
  );
}
```

### Example 4: React 19 Form with Actions

```jsx
"use client";
import { useActionState, useOptimistic } from "react";

async function addComment(prevState, formData) {
  "use server";
  const text = formData.get("comment");
  if (!text?.trim()) return { error: "Comment cannot be empty" };

  try {
    const comment = await db.comments.create({ text, createdAt: new Date() });
    revalidatePath("/post");
    return { error: null, success: true };
  } catch (err) {
    return { error: "Failed to add comment" };
  }
}

function CommentSection({ comments }) {
  const [state, formAction, isPending] = useActionState(addComment, {
    error: null,
  });
  const [optimisticComments, addOptimistic] = useOptimistic(
    comments,
    (current, newComment) => [...current, { ...newComment, pending: true }],
  );

  return (
    <div>
      <ul>
        {optimisticComments.map((c, i) => (
          <li key={c.id || i} style={{ opacity: c.pending ? 0.5 : 1 }}>
            {c.text}
          </li>
        ))}
      </ul>

      <form
        action={async (formData) => {
          addOptimistic({ text: formData.get("comment"), id: Date.now() });
          await formAction(formData);
        }}
      >
        <textarea name="comment" required />
        <button disabled={isPending}>
          {isPending ? "Posting..." : "Post Comment"}
        </button>
        {state.error && <p className="error">{state.error}</p>}
      </form>
    </div>
  );
}
```

### Example 5: Tabbed Interface with Activity (React 19.2)

```jsx
import { Activity, useState } from "react";

function ExpensiveChart({ data }) {
  // Imagine this component takes 500ms+ to initialize
  const [zoom, setZoom] = useState(1);
  return (
    <div>
      <h3>Analytics Chart (zoom: {zoom}x)</h3>
      <button onClick={() => setZoom((z) => z + 0.5)}>Zoom In</button>
      <div style={{ height: 300 }}>
        {/* Chart renders here — state preserved when hidden */}
      </div>
    </div>
  );
}

function TabApp() {
  const [tab, setTab] = useState("dashboard");

  return (
    <div>
      <nav>
        {["dashboard", "analytics", "settings"].map((t) => (
          <button
            key={t}
            onClick={() => setTab(t)}
            style={{ fontWeight: tab === t ? "bold" : "normal" }}
          >
            {t}
          </button>
        ))}
      </nav>

      {/* Activity preserves state — switching tabs is instant */}
      <Activity mode={tab === "dashboard" ? "visible" : "hidden"}>
        <h2>Dashboard</h2>
        <p>Welcome back!</p>
      </Activity>
      <Activity mode={tab === "analytics" ? "visible" : "hidden"}>
        <ExpensiveChart data={[]} />
      </Activity>
      <Activity mode={tab === "settings" ? "visible" : "hidden"}>
        <h2>Settings</h2>
        <p>Configure your preferences.</p>
      </Activity>
    </div>
  );
}

export default TabApp;
```

---

## 10. Go Deeper — Resources

- [Official React Docs (react.dev)](https://react.dev) — The definitive guide; interactive tutorials
- [React 19 Blog Post](https://react.dev/blog/2024/12/05/react-19) — Official release notes
- [React 19.2 Release Notes](https://react.dev/blog) — Activity, cacheSignal, and more
- [React Compiler Docs](https://react.dev/learn/react-compiler) — Installation and usage guide
- [Next.js Documentation](https://nextjs.org/docs) — Full-stack React framework
- [React TypeScript Cheatsheet](https://react-typescript-cheatsheet.netlify.app/) — TS patterns
- [Bulletproof React](https://github.com/alan2207/bulletproof-react) — Production architecture guide
- [React Patterns](https://reactpatterns.com/) — Common pattern reference
- [TanStack Query Docs](https://tanstack.com/query/latest) — Server state management
- [Zustand Docs](https://zustand.docs.pmnd.rs/) — Lightweight state management
- [Epic React (Kent C. Dodds)](https://epicreact.dev/) — Advanced React training
- [React DevTools](https://react.dev/learn/react-developer-tools) — Browser extension for debugging
- [Vite Guide](https://vitejs.dev/guide/) — Modern build tool for React apps

---

_Generated by skill-learning. Reload this file to restore expert-level knowledge of React._
_Source: web-research_
