# React.js Scenario-Based Machine Test
## 30 Questions with Solutions and Explanations

**Level:** Beginner to Intermediate  
**Technology:** React.js, JavaScript, React Hooks, React Router  
**Purpose:** Company placement preparation, coding assessments, and practical machine tests

> These are representative practice questions based on common React development tasks. They are not claimed to be exact questions from specific companies.

## Table of Contents

1. Attendance Counter
2. Student Live Search
3. Reusable Student Card
4. Fetch Products from an API
5. To-Do List
6. Show/Hide Password
7. Shopping Cart Total
8. Dark Mode with Context API
9. Department Filter
10. Navigation with React Router
11. Product Search, Filter, and Sort
12. Debounced Search
13. Registration Form Validation
14. Pagination
15. Fix State Mutation
16. Child-to-Parent Communication
17. Fetch Data with Request Cleanup
18. Confirmation Modal
19. Optimize Calculation with `useMemo`
20. Protected Route
21. Reusable Button Component
22. Character Counter
23. Select All Checkboxes
24. Star Rating
25. Accordion
26. Custom Hook
27. Theme with Context API
28. Prevent Duplicate Submission
29. Stopwatch
30. Sortable Employee Table

---

# Part 1: Core React Questions

## Q1. Build an Attendance Counter

**Scenario:** A teacher wants to count the number of students present. Add buttons to increase, decrease, and reset the count. The count must never become negative.

### Solution

```jsx
import { useState } from "react";

export default function AttendanceCounter() {
  const [count, setCount] = useState(0);

  return (
    <div>
      <h2>Students Present: {count}</h2>

      <button onClick={() => setCount(c => c + 1)}>
        Add Student
      </button>

      <button
        onClick={() => setCount(c => Math.max(0, c - 1))}
      >
        Remove Student
      </button>

      <button onClick={() => setCount(0)}>
        Reset
      </button>
    </div>
  );
}
```

### Explanation

- `useState(0)` creates state with an initial value of zero.
- `setCount` updates the count.
- The functional update `c => c + 1` uses the previous state.
- `Math.max(0, c - 1)` prevents negative values.
- React re-renders the component when state changes.

**Concepts:** `useState`, event handlers, functional state updates.

## Q2. Implement Student Live Search

**Scenario:** Display a list of students. When the user types a name, show only matching students.

### Solution

```jsx
import { useState } from "react";

const students = [
  { id: 1, name: "Anu" },
  { id: 2, name: "Rahul" },
  { id: 3, name: "Maya" },
  { id: 4, name: "Abin" }
];

export default function StudentSearch() {
  const [search, setSearch] = useState("");

  const filteredStudents = students.filter(student =>
    student.name.toLowerCase().includes(search.toLowerCase())
  );

  return (
    <div>
      <input
        value={search}
        onChange={e => setSearch(e.target.value)}
        placeholder="Search students"
      />

      {filteredStudents.length === 0 && <p>No students found.</p>}

      {filteredStudents.map(student => (
        <p key={student.id}>{student.name}</p>
      ))}
    </div>
  );
}
```

### Explanation

- The input is controlled by React state.
- `filter()` creates a list of matching students.
- `toLowerCase()` makes searching case-insensitive.
- `map()` renders the filtered list.
- Each rendered item has a unique `key`.

**Concepts:** Controlled inputs, `filter`, `map`, keys.

## Q3. Create a Reusable Student Card

**Scenario:** Create one component that displays a student's name, department, and mark. Reuse it for multiple students.

### Solution

```jsx
function StudentCard({ name, department, mark }) {
  return (
    <article>
      <h3>{name}</h3>
      <p>Department: {department}</p>
      <p>Mark: {mark}</p>
    </article>
  );
}

export default function App() {
  return (
    <div>
      <StudentCard name="Anu" department="CSE" mark={90} />
      <StudentCard name="Rahul" department="ECE" mark={85} />
      <StudentCard name="Maya" department="EEE" mark={95} />
    </div>
  );
}
```

### Explanation

- A component is a reusable piece of UI.
- Props pass information from a parent to a child.
- The same component can display different data.
- Numbers such as `mark={90}` are passed as numeric values.

**Concepts:** Components, props, reusability.

## Q4. Fetch Products from an API

**Scenario:** Fetch products when the component loads. Display loading, error, and success states.

### Solution

```jsx
import { useEffect, useState } from "react";

export default function Products() {
  const [products, setProducts] = useState([]);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState("");

  useEffect(() => {
    async function loadProducts() {
      try {
        const response = await fetch(
          "https://jsonplaceholder.typicode.com/posts"
        );

        if (!response.ok) {
          throw new Error("Failed to fetch products");
        }

        const data = await response.json();
        setProducts(data.slice(0, 5));
      } catch (err) {
        setError(err.message);
      } finally {
        setLoading(false);
      }
    }

    loadProducts();
  }, []);

  if (loading) return <p>Loading...</p>;
  if (error) return <p>{error}</p>;

  return (
    <ul>
      {products.map(product => (
        <li key={product.id}>{product.title}</li>
      ))}
    </ul>
  );
}
```

### Explanation

- `useEffect` runs the loading operation after the component mounts.
- An empty dependency array means the effect does not re-run because of ordinary state changes.
- `fetch()` sends the HTTP request.
- `response.json()` parses the response body.
- `finally` ensures loading is stopped after success or failure.

**Concepts:** `useEffect`, `fetch`, `async/await`, loading states, error handling.

## Q5. Build a To-Do List

**Scenario:** Users should add tasks and delete individual tasks.

### Solution

```jsx
import { useState } from "react";

export default function TodoApp() {
  const [task, setTask] = useState("");
  const [tasks, setTasks] = useState([]);

  function addTask(e) {
    e.preventDefault();

    const value = task.trim();
    if (!value) return;

    setTasks(previous => [
      ...previous,
      { id: crypto.randomUUID(), text: value }
    ]);

    setTask("");
  }

  function deleteTask(id) {
    setTasks(previous =>
      previous.filter(item => item.id !== id)
    );
  }

  return (
    <div>
      <form onSubmit={addTask}>
        <input
          value={task}
          onChange={e => setTask(e.target.value)}
          placeholder="Enter task"
        />
        <button type="submit">Add</button>
      </form>

      {tasks.map(item => (
        <div key={item.id}>
          <span>{item.text}</span>
          <button onClick={() => deleteTask(item.id)}>
            Delete
          </button>
        </div>
      ))}
    </div>
  );
}
```

### Explanation

- `preventDefault()` prevents the form from reloading the page.
- The spread operator creates a new array.
- `filter()` removes the selected task.
- A unique ID is used instead of an array index.

**Concepts:** Forms, state, immutable updates, `filter`, keys.

## Q6. Show and Hide a Password

**Scenario:** Add a button to toggle password visibility.

### Solution

```jsx
import { useState } from "react";

export default function PasswordInput() {
  const [visible, setVisible] = useState(false);
  const [password, setPassword] = useState("");

  return (
    <div>
      <input
        type={visible ? "text" : "password"}
        value={password}
        onChange={e => setPassword(e.target.value)}
        placeholder="Enter password"
      />

      <button
        type="button"
        onClick={() => setVisible(v => !v)}
      >
        {visible ? "Hide" : "Show"}
      </button>
    </div>
  );
}
```

### Explanation

The conditional expression chooses the input type. When `visible` is true, the input uses `text`; otherwise, it uses `password`.

**Concepts:** Conditional rendering, boolean state, event handling.

## Q7. Calculate a Shopping Cart Total

**Scenario:** Display product prices and calculate their total.

### Solution

```jsx
const cart = [
  { id: 1, name: "Keyboard", price: 1500, quantity: 2 },
  { id: 2, name: "Mouse", price: 700, quantity: 1 },
  { id: 3, name: "Monitor", price: 9000, quantity: 1 }
];

export default function ShoppingCart() {
  const total = cart.reduce(
    (sum, item) => sum + item.price * item.quantity,
    0
  );

  return (
    <div>
      <h2>Shopping Cart</h2>

      {cart.map(item => (
        <p key={item.id}>
          {item.name} × {item.quantity}: ₹
          {item.price * item.quantity}
        </p>
      ))}

      <h3>Total: ₹{total}</h3>
    </div>
  );
}
```

### Explanation

`reduce()` combines all item totals into one number. Here, each product's price is multiplied by its quantity.

**Concepts:** `reduce`, arrays, derived values.

## Q8. Implement Dark Mode Using Context API

**Scenario:** Several components need access to the same theme without passing props through every component.

### Solution

```jsx
import { createContext, useContext, useState } from "react";

const ThemeContext = createContext(null);

function ThemeButton() {
  const { theme, toggleTheme } = useContext(ThemeContext);

  return (
    <button onClick={toggleTheme}>
      Current theme: {theme}. Toggle
    </button>
  );
}

export default function App() {
  const [theme, setTheme] = useState("light");

  function toggleTheme() {
    setTheme(current =>
      current === "light" ? "dark" : "light"
    );
  }

  return (
    <ThemeContext.Provider value={{ theme, toggleTheme }}>
      <main
        style={{
          background: theme === "dark" ? "#222" : "#fff",
          color: theme === "dark" ? "#fff" : "#222",
          padding: 20
        }}
      >
        <h1>Theme Demo</h1>
        <ThemeButton />
      </main>
    </ThemeContext.Provider>
  );
}
```

### Explanation

- `createContext()` creates a context.
- The provider makes the value available to descendants.
- `useContext()` reads the value without manually passing props through every level.

**Concepts:** Context API, `useContext`, shared state.

## Q9. Filter Students by Department

**Scenario:** Show all students initially. When the user selects a department, show only students from that department.

### Solution

```jsx
import { useState } from "react";

const students = [
  { id: 1, name: "Anu", department: "CSE" },
  { id: 2, name: "Rahul", department: "ECE" },
  { id: 3, name: "Maya", department: "CSE" },
  { id: 4, name: "John", department: "EEE" }
];

export default function DepartmentFilter() {
  const [department, setDepartment] = useState("All");

  const filtered = students.filter(student =>
    department === "All" ||
    student.department === department
  );

  return (
    <div>
      <select
        value={department}
        onChange={e => setDepartment(e.target.value)}
      >
        <option value="All">All Departments</option>
        <option value="CSE">CSE</option>
        <option value="ECE">ECE</option>
        <option value="EEE">EEE</option>
      </select>

      {filtered.map(student => (
        <p key={student.id}>
          {student.name} — {student.department}
        </p>
      ))}
    </div>
  );
}
```

### Explanation

The selected department is stored in state. The list is derived by filtering the original data.

**Concepts:** Controlled selects, `filter`, conditional logic.

## Q10. Navigate Between Pages Using React Router

**Scenario:** Build Home and Profile pages. Navigate between them without a full-page browser reload.

Install the package:

```bash
npm install react-router-dom
```

### Solution

```jsx
import {
  BrowserRouter,
  Routes,
  Route,
  Link
} from "react-router-dom";

function Home() {
  return <h2>Home Page</h2>;
}

function Profile() {
  return <h2>Profile Page</h2>;
}

export default function App() {
  return (
    <BrowserRouter>
      <nav>
        <Link to="/">Home</Link>{" | "}
        <Link to="/profile">Profile</Link>
      </nav>

      <Routes>
        <Route path="/" element={<Home />} />
        <Route path="/profile" element={<Profile />} />
      </Routes>
    </BrowserRouter>
  );
}
```

### Explanation

- `BrowserRouter` enables client-side routing.
- `Routes` contains the route definitions.
- `Route` maps a URL path to a component.
- `Link` navigates without performing a normal full-page navigation.

**Concepts:** React Router, SPA navigation, components.

---

# Part 2: Intermediate React Machine-Test Questions

## Q11. Product Search, Filter, and Sort

**Scenario:** Display products with a search box, category filter, and price sorting.

### Solution

```jsx
import { useState } from "react";

const products = [
  { id: 1, name: "Laptop", category: "Electronics", price: 50000 },
  { id: 2, name: "Phone", category: "Electronics", price: 20000 },
  { id: 3, name: "Chair", category: "Furniture", price: 3000 }
];

export default function Products() {
  const [search, setSearch] = useState("");
  const [category, setCategory] = useState("All");
  const [sort, setSort] = useState("low");

  const filtered = products
    .filter(p =>
      p.name.toLowerCase().includes(search.toLowerCase())
    )
    .filter(p =>
      category === "All" || p.category === category
    )
    .sort((a, b) =>
      sort === "low"
        ? a.price - b.price
        : b.price - a.price
    );

  return (
    <div>
      <input
        value={search}
        onChange={e => setSearch(e.target.value)}
        placeholder="Search products"
      />

      <select
        value={category}
        onChange={e => setCategory(e.target.value)}
      >
        <option>All</option>
        <option>Electronics</option>
        <option>Furniture</option>
      </select>

      <select value={sort} onChange={e => setSort(e.target.value)}>
        <option value="low">Price: Low to High</option>
        <option value="high">Price: High to Low</option>
      </select>

      {filtered.map(p => (
        <p key={p.id}>{p.name} — ₹{p.price}</p>
      ))}
    </div>
  );
}
```

### Explanation

Search and category filtering are applied before sorting. Because `filter()` creates a new array, sorting that filtered array does not mutate the original products array.

**Concepts:** `useState`, `filter`, `sort`, controlled inputs.

## Q12. Implement Debounced Search

**Scenario:** Call a search operation only after the user stops typing for 500 milliseconds.

### Solution

```jsx
import { useEffect, useState } from "react";

export default function Search() {
  const [query, setQuery] = useState("");
  const [debounced, setDebounced] = useState("");

  useEffect(() => {
    const timer = setTimeout(() => {
      setDebounced(query);
    }, 500);

    return () => clearTimeout(timer);
  }, [query]);

  useEffect(() => {
    if (debounced.trim()) {
      console.log("Search API called:", debounced);
    }
  }, [debounced]);

  return (
    <input
      value={query}
      onChange={e => setQuery(e.target.value)}
      placeholder="Search..."
    />
  );
}
```

### Explanation

Each time the query changes, the first effect schedules a timer. Cleanup cancels the previous timer. The search operation runs after the user pauses typing.

**Concepts:** `useEffect`, cleanup, debouncing.

## Q13. Validate a Registration Form

**Scenario:** Validate the name, email, and password before accepting a registration.

### Solution

```jsx
import { useState } from "react";

export default function Register() {
  const [form, setForm] = useState({
    name: "",
    email: "",
    password: ""
  });

  const [error, setError] = useState("");

  function updateField(field, value) {
    setForm(previous => ({
      ...previous,
      [field]: value
    }));
  }

  function handleSubmit(e) {
    e.preventDefault();

    if (!form.name.trim()) {
      setError("Name is required");
    } else if (!/^\S+@\S+\.\S+$/.test(form.email)) {
      setError("Enter a valid email");
    } else if (form.password.length < 8) {
      setError("Password must be at least 8 characters");
    } else {
      setError("");
      alert("Registration successful");
    }
  }

  return (
    <form onSubmit={handleSubmit}>
      <input
        value={form.name}
        onChange={e => updateField("name", e.target.value)}
        placeholder="Name"
      />

      <input
        type="email"
        value={form.email}
        onChange={e => updateField("email", e.target.value)}
        placeholder="Email"
      />

      <input
        type="password"
        value={form.password}
        onChange={e => updateField("password", e.target.value)}
        placeholder="Password"
      />

      <button type="submit">Register</button>
      {error && <p role="alert">{error}</p>}
    </form>
  );
}
```

### Explanation

The form values are controlled by React. Validation runs during submission, and the error message is displayed conditionally.

**Concepts:** Form handling, validation, state objects, `preventDefault`.

## Q14. Implement Pagination

**Scenario:** Display five users per page with Previous and Next buttons.

### Solution

```jsx
import { useState } from "react";

const users = Array.from({ length: 23 }, (_, i) => ({
  id: i + 1,
  name: `User ${i + 1}`
}));

export default function Pagination() {
  const [page, setPage] = useState(1);
  const perPage = 5;
  const totalPages = Math.ceil(users.length / perPage);

  const visible = users.slice(
    (page - 1) * perPage,
    page * perPage
  );

  return (
    <div>
      {visible.map(user => (
        <p key={user.id}>{user.name}</p>
      ))}

      <button
        disabled={page === 1}
        onClick={() => setPage(p => p - 1)}
      >
        Previous
      </button>

      <span> Page {page} of {totalPages} </span>

      <button
        disabled={page === totalPages}
        onClick={() => setPage(p => p + 1)}
      >
        Next
      </button>
    </div>
  );
}
```

### Explanation

- `Math.ceil()` calculates the number of pages.
- `slice()` selects the current page's records.
- Buttons are disabled when the user reaches either end.

**Concepts:** Pagination, `slice`, functional updates.

## Q15. Fix the State Mutation Bug

**Scenario:** Identify and fix the issue in this code.

Incorrect implementation:

```jsx
const [items, setItems] = useState(["Apple", "Orange"]);

function addItem() {
  items.push("Mango");
  setItems(items);
}
```

### Correct solution

```jsx
function addItem() {
  setItems(previous => [...previous, "Mango"]);
}
```

### Explanation

`push()` modifies the existing array. React state should be updated immutably, creating a new array reference instead.

**Concepts:** Immutability, state references, functional updates.

## Q16. Send Data from a Child Component to Its Parent

**Scenario:** A child component has a button. Clicking it should update a message in the parent.

### Solution

```jsx
import { useState } from "react";

function Child({ onNotify }) {
  return (
    <button onClick={() => onNotify("Hello from child!")}>
      Send Message
    </button>
  );
}

export default function Parent() {
  const [message, setMessage] = useState("No message");

  return (
    <div>
      <p>{message}</p>
      <Child onNotify={setMessage} />
    </div>
  );
}
```

### Explanation

The parent passes a callback as a prop. The child calls that function to notify the parent.

**Concepts:** Props, callbacks, lifting state up.

## Q17. Fetch Data and Cancel Outdated Requests

**Scenario:** Fetch user details when the user ID changes. Cancel the previous request when necessary.

### Solution

```jsx
import { useEffect, useState } from "react";

export default function User({ userId }) {
  const [user, setUser] = useState(null);
  const [error, setError] = useState("");

  useEffect(() => {
    const controller = new AbortController();

    async function loadUser() {
      try {
        setError("");
        setUser(null);

        const response = await fetch(
          `https://jsonplaceholder.typicode.com/users/${userId}`,
          { signal: controller.signal }
        );

        if (!response.ok) {
          throw new Error("Failed to load user");
        }

        setUser(await response.json());
      } catch (err) {
        if (err.name !== "AbortError") {
          setError(err.message);
        }
      }
    }

    loadUser();

    return () => controller.abort();
  }, [userId]);

  if (error) return <p>{error}</p>;
  if (!user) return <p>Loading...</p>;

  return <h2>{user.name}</h2>;
}
```

### Explanation

`AbortController` allows the effect to cancel its fetch request during cleanup. This helps prevent outdated requests from unnecessarily continuing when the component's inputs change.

**Concepts:** Effects, API requests, cleanup, `AbortController`.

## Q18. Build a Confirmation Modal

**Scenario:** Clicking Delete opens a confirmation dialog. Delete the item only after the user confirms.

### Solution

```jsx
import { useState } from "react";

export default function DeleteExample() {
  const [showModal, setShowModal] = useState(false);
  const [deleted, setDeleted] = useState(false);

  return (
    <div>
      {!deleted && (
        <button onClick={() => setShowModal(true)}>
          Delete item
        </button>
      )}

      {showModal && (
        <div role="dialog" aria-modal="true">
          <p>Are you sure you want to delete this item?</p>

          <button onClick={() => {
            setDeleted(true);
            setShowModal(false);
          }}>
            Confirm
          </button>

          <button onClick={() => setShowModal(false)}>
            Cancel
          </button>
        </div>
      )}

      {deleted && <p>Item deleted</p>}
    </div>
  );
}
```

### Explanation

The modal is conditionally rendered. Confirming updates the deleted state; cancelling simply closes the modal.

**Concepts:** Conditional rendering, multiple state variables.

> A production modal should also support keyboard navigation, focus management, and an accessible close mechanism.

## Q19. Optimize an Expensive Calculation with `useMemo`

**Scenario:** Calculate the total of several products. Avoid recalculating the total when unrelated state changes.

### Solution

```jsx
import { useMemo, useState } from "react";

export default function Cart() {
  const [count, setCount] = useState(0);

  const [products] = useState([
    { id: 1, price: 500 },
    { id: 2, price: 1000 },
    { id: 3, price: 250 }
  ]);

  const total = useMemo(() => {
    console.log("Calculating total...");
    return products.reduce((sum, p) => sum + p.price, 0);
  }, [products]);

  return (
    <div>
      <p>Total: ₹{total}</p>
      <p>Counter: {count}</p>

      <button onClick={() => setCount(c => c + 1)}>
        Increase counter
      </button>
    </div>
  );
}
```

### Explanation

`useMemo` caches a calculated value while its dependencies remain unchanged.

**Concepts:** Memoization, derived data, dependency arrays.

> Use `useMemo` when it provides a meaningful optimization. Simple calculations usually do not need it.

## Q20. Create a Protected Route

**Scenario:** Only logged-in users should access the Dashboard.

Install React Router:

```bash
npm install react-router-dom
```

### Solution

```jsx
import {
  BrowserRouter,
  Routes,
  Route,
  Navigate
} from "react-router-dom";

function ProtectedRoute({ isLoggedIn, children }) {
  return isLoggedIn
    ? children
    : <Navigate to="/login" replace />;
}

function Login() {
  return <h1>Login Page</h1>;
}

function Dashboard() {
  return <h1>Dashboard</h1>;
}

export default function App() {
  const isLoggedIn = false;

  return (
    <BrowserRouter>
      <Routes>
        <Route path="/login" element={<Login />} />

        <Route
          path="/dashboard"
          element={
            <ProtectedRoute isLoggedIn={isLoggedIn}>
              <Dashboard />
            </ProtectedRoute>
          }
        />
      </Routes>
    </BrowserRouter>
  );
}
```

### Explanation

The protected route checks the authentication state. If the user is not logged in, it redirects them to the login page.

**Concepts:** React Router, conditional navigation, component composition.

> This example demonstrates frontend routing only. Real applications must enforce authorization on the server as well.

---

# Part 3: Additional Practical Challenges

## Q21. Create a Reusable Button Component

**Scenario:** Create one Button component that supports different labels, colors, disabled states, and click handlers.

### Solution

```jsx
function Button({
  children,
  onClick,
  disabled = false,
  variant = "primary"
}) {
  const colors = {
    primary: "#2563eb",
    danger: "#dc2626",
    success: "#16a34a"
  };

  return (
    <button
      disabled={disabled}
      onClick={onClick}
      style={{
        backgroundColor: colors[variant] ?? colors.primary,
        color: "white",
        padding: "10px 16px",
        border: 0,
        borderRadius: 6,
        cursor: disabled ? "not-allowed" : "pointer"
      }}
    >
      {children}
    </button>
  );
}

export default function App() {
  return (
    <>
      <Button onClick={() => alert("Saved")}>Save</Button>

      <Button
        variant="danger"
        onClick={() => alert("Deleted")}
      >
        Delete
      </Button>

      <Button disabled>Unavailable</Button>
    </>
  );
}
```

### Explanation

The component accepts props for behavior and appearance. The `children` prop lets the caller supply button content.

**Concepts:** Reusable components, props, default values, `children`.

## Q22. Implement a Character Counter

**Scenario:** Allow a maximum of 100 characters in a text area and show how many characters remain.

### Solution

```jsx
import { useState } from "react";

export default function CharacterCounter() {
  const [text, setText] = useState("");
  const limit = 100;

  return (
    <div>
      <textarea
        value={text}
        maxLength={limit}
        onChange={e => setText(e.target.value)}
      />

      <p>{limit - text.length} characters remaining</p>
    </div>
  );
}
```

### Explanation

The controlled textarea stores its current value in state. `maxLength` prevents input beyond the specified limit.

**Concepts:** Controlled text areas, string length, state.

## Q23. Select All Checkboxes

**Scenario:** A task list should allow selecting every task or changing individual selections.

### Solution

```jsx
import { useState } from "react";

const initialTasks = [
  { id: 1, name: "Learn React", checked: false },
  { id: 2, name: "Practice hooks", checked: false },
  { id: 3, name: "Build a project", checked: false }
];

export default function TaskList() {
  const [tasks, setTasks] = useState(initialTasks);

  const allSelected =
    tasks.length > 0 && tasks.every(task => task.checked);

  function toggleAll(checked) {
    setTasks(tasks.map(task => ({ ...task, checked })));
  }

  function toggleTask(id) {
    setTasks(tasks.map(task =>
      task.id === id
        ? { ...task, checked: !task.checked }
        : task
    ));
  }

  return (
    <div>
      <label>
        <input
          type="checkbox"
          checked={allSelected}
          onChange={e => toggleAll(e.target.checked)}
        />
        Select all
      </label>

      {tasks.map(task => (
        <label key={task.id} style={{ display: "block" }}>
          <input
            type="checkbox"
            checked={task.checked}
            onChange={() => toggleTask(task.id)}
          />
          {task.name}
        </label>
      ))}
    </div>
  );
}
```

### Explanation

`every()` checks whether all tasks are selected. `map()` creates updated task objects without mutating the existing array.

**Concepts:** Checkbox state, `every`, `map`, immutability.

## Q24. Build a Star-Rating Component

**Scenario:** Allow users to rate a product from one to five stars.

### Solution

```jsx
import { useState } from "react";

export default function Rating() {
  const [rating, setRating] = useState(0);

  return (
    <div>
      {[1, 2, 3, 4, 5].map(star => (
        <button
          key={star}
          onClick={() => setRating(star)}
          aria-label={`Rate ${star} out of 5`}
          style={{
            fontSize: 28,
            border: 0,
            background: "transparent",
            color: star <= rating ? "orange" : "gray"
          }}
        >
          ★
        </button>
      ))}

      <p>Your rating: {rating}/5</p>
    </div>
  );
}
```

### Explanation

Each button represents a rating value. Selecting a star updates state, and the conditional style highlights all stars up to the chosen value.

**Concepts:** State, list rendering, conditional styling.

## Q25. Build an Accordion

**Scenario:** Display multiple FAQ questions, but allow only one answer to be open at a time.

### Solution

```jsx
import { useState } from "react";

const faqs = [
  { id: 1, question: "What is React?", answer: "A UI library." },
  {
    id: 2,
    question: "What is JSX?",
    answer: "A syntax extension for JavaScript."
  },
  {
    id: 3,
    question: "What are hooks?",
    answer: "Functions that let components use React features."
  }
];

export default function Accordion() {
  const [openId, setOpenId] = useState(null);

  return (
    <div>
      {faqs.map(faq => (
        <section key={faq.id}>
          <button
            onClick={() =>
              setOpenId(openId === faq.id ? null : faq.id)
            }
            aria-expanded={openId === faq.id}
          >
            {faq.question}
          </button>

          {openId === faq.id && <p>{faq.answer}</p>}
        </section>
      ))}
    </div>
  );
}
```

### Explanation

The state contains the ID of the currently open FAQ. Clicking the same question closes it; clicking another question opens that answer instead.

**Concepts:** Conditional rendering, toggling state, unique IDs.

## Q26. Create a Custom Hook

**Scenario:** Several components need to know whether the browser is online. Avoid repeating the event-listener logic.

### Solution

```jsx
import { useEffect, useState } from "react";

function useOnlineStatus() {
  const [online, setOnline] = useState(
    () => navigator.onLine
  );

  useEffect(() => {
    const goOnline = () => setOnline(true);
    const goOffline = () => setOnline(false);

    window.addEventListener("online", goOnline);
    window.addEventListener("offline", goOffline);

    return () => {
      window.removeEventListener("online", goOnline);
      window.removeEventListener("offline", goOffline);
    };
  }, []);

  return online;
}

export default function Status() {
  const online = useOnlineStatus();

  return <h2>{online ? "Online" : "Offline"}</h2>;
}
```

### Explanation

A custom hook extracts reusable stateful logic. The hook registers browser event listeners and removes them during cleanup.

**Concepts:** Custom hooks, effects, event listeners, cleanup.

## Q27. Share a Theme Using Context API

**Scenario:** Multiple components need access to a shared theme without passing props through every component.

### Solution

```jsx
import { createContext, useContext, useState } from "react";

const ThemeContext = createContext(null);

function Toolbar() {
  const { theme, toggleTheme } = useContext(ThemeContext);

  return (
    <div>
      <p>Current theme: {theme}</p>
      <button onClick={toggleTheme}>Toggle theme</button>
    </div>
  );
}

export default function App() {
  const [theme, setTheme] = useState("light");

  const value = {
    theme,
    toggleTheme: () =>
      setTheme(current =>
        current === "light" ? "dark" : "light"
      )
  };

  return (
    <ThemeContext.Provider value={value}>
      <Toolbar />
    </ThemeContext.Provider>
  );
}
```

### Explanation

Context provides a shared value to descendant components. `useContext` reads that value, and updating the provider's state causes consumers to receive the new value.

**Concepts:** `createContext`, `useContext`, shared state.

## Q28. Prevent Duplicate Form Submission

**Scenario:** A user clicks Submit repeatedly while an API request is in progress. Prevent duplicate submissions in the UI.

### Solution

```jsx
import { useState } from "react";

export default function SubmitForm() {
  const [loading, setLoading] = useState(false);
  const [message, setMessage] = useState("");

  async function handleSubmit(e) {
    e.preventDefault();

    if (loading) return;

    setLoading(true);
    setMessage("");

    try {
      // Replace with a real API request.
      await new Promise(resolve => setTimeout(resolve, 1000));
      setMessage("Submitted successfully");
    } catch {
      setMessage("Submission failed");
    } finally {
      setLoading(false);
    }
  }

  return (
    <form onSubmit={handleSubmit}>
      <button disabled={loading}>
        {loading ? "Submitting..." : "Submit"}
      </button>

      <p>{message}</p>
    </form>
  );
}
```

### Explanation

The loading state disables the button during submission. The `finally` block resets the loading state whether the operation succeeds or fails.

**Concepts:** Async functions, loading states, `try/catch/finally`.

> For payments and other critical operations, the backend should also use an idempotency mechanism. A disabled button alone cannot guarantee that duplicate requests will never be processed.

## Q29. Implement a Stopwatch

**Scenario:** Build a stopwatch with Start, Stop, and Reset buttons.

### Solution

```jsx
import { useEffect, useState } from "react";

export default function Stopwatch() {
  const [running, setRunning] = useState(false);
  const [seconds, setSeconds] = useState(0);

  useEffect(() => {
    if (!running) return;

    const timer = setInterval(() => {
      setSeconds(s => s + 1);
    }, 1000);

    return () => clearInterval(timer);
  }, [running]);

  const minutes = Math.floor(seconds / 60);
  const remainingSeconds = seconds % 60;

  return (
    <div>
      <h2>
        {minutes}:{String(remainingSeconds).padStart(2, "0")}
      </h2>

      <button
        onClick={() => setRunning(true)}
        disabled={running}
      >
        Start
      </button>

      <button
        onClick={() => setRunning(false)}
        disabled={!running}
      >
        Stop
      </button>

      <button
        onClick={() => {
          setRunning(false);
          setSeconds(0);
        }}
      >
        Reset
      </button>
    </div>
  );
}
```

### Explanation

The effect starts an interval while the stopwatch is running. Cleanup clears the interval when the stopwatch stops or the effect is re-run.

**Concepts:** `useEffect`, intervals, cleanup, functional state updates.

## Q30. Build a Sortable Employee Table

**Scenario:** Display employees and allow sorting by name or salary when a column header is clicked.

### Solution

```jsx
import { useState } from "react";

const employees = [
  { id: 1, name: "Anu", salary: 30000 },
  { id: 2, name: "Rahul", salary: 45000 },
  { id: 3, name: "Maya", salary: 25000 }
];

export default function EmployeeTable() {
  const [sortBy, setSortBy] = useState("name");
  const [ascending, setAscending] = useState(true);

  const sorted = [...employees].sort((a, b) => {
    const result =
      typeof a[sortBy] === "string"
        ? a[sortBy].localeCompare(b[sortBy])
        : a[sortBy] - b[sortBy];

    return ascending ? result : -result;
  });

  function changeSort(field) {
    if (sortBy === field) {
      setAscending(value => !value);
    } else {
      setSortBy(field);
      setAscending(true);
    }
  }

  return (
    <table>
      <thead>
        <tr>
          <th>
            <button onClick={() => changeSort("name")}>
              Name
            </button>
          </th>
          <th>
            <button onClick={() => changeSort("salary")}>
              Salary
            </button>
          </th>
        </tr>
      </thead>

      <tbody>
        {sorted.map(employee => (
          <tr key={employee.id}>
            <td>{employee.name}</td>
            <td>₹{employee.salary}</td>
          </tr>
        ))}
      </tbody>
    </table>
  );
}
```

### Explanation

- `[...employees]` creates a copy before sorting.
- `localeCompare()` compares strings.
- Numeric subtraction compares salaries.
- Clicking the same column reverses the sort direction.

**Concepts:** Sorting, state, array copying, dynamic property access.

---

# Part 4: Quick React Revision Notes

## Important Hooks

| Hook | Purpose | Example use |
|---|---|---|
| `useState` | Store component state | Counter, form, toggle |
| `useEffect` | Synchronize with external systems | API calls, event listeners |
| `useContext` | Read shared context | Theme, authentication state |
| `useMemo` | Cache a calculated value | Expensive calculations |
| `useCallback` | Cache a function definition | Optimized child props |
| Custom hooks | Reuse stateful logic | Online status, form logic |

## Important JavaScript Methods

| Method | Purpose | Example |
|---|---|---|
| `map()` | Transform or render items | Display student names |
| `filter()` | Select matching items | Search students |
| `reduce()` | Combine values | Calculate cart total |
| `find()` | Find one matching item | Find a user by ID |
| `some()` | Check whether any item matches | Check for overdue tasks |
| `every()` | Check whether all items match | Select-all checkbox |
| `sort()` | Sort array items | Sort employees by salary |
| `slice()` | Extract part of an array | Pagination |

## Common React Mistakes

1. **Mutating state:** Avoid `array.push()` or modifying existing state objects. Create new arrays or objects.
2. **Missing keys:** Give mapped elements stable, unique keys.
3. **Incorrect effect dependencies:** Include values used by an effect when they affect its behavior.
4. **Forgetting cleanup:** Clear timers and remove event listeners when necessary.
5. **Uncontrolled form logic:** Use controlled inputs when the UI needs to reflect and validate React state.
6. **Direct DOM manipulation:** Prefer React state and declarative rendering for ordinary UI updates.
7. **Unnecessary memoization:** Do not add `useMemo` and `useCallback` everywhere without a reason.
8. **Confusing props and state:** Props come from the parent; state is managed by the component or a state-management mechanism.
9. **Ignoring errors:** API calls should handle loading, failure, and success states.
10. **Frontend-only security:** Client-side route checks do not replace server-side authorization.

---

# Part 5: Machine-Test Evaluation Rubric

Use this rubric to assess your own solutions or a candidate's performance.

| Criterion | Marks |
|---|---:|
| Correct functionality | 40 |
| Proper use of React state and hooks | 20 |
| Clean component structure and reusable code | 15 |
| Input validation and edge cases | 10 |
| Readability and naming | 10 |
| Basic accessibility | 5 |
| **Total** | **100** |

## Suggested Practice Plan

| Session | Questions | Focus |
|---|---|---|
| 1 | 1–5 | State, props, forms, lists |
| 2 | 6–10 | Conditional UI, calculations, routing |
| 3 | 11–15 | Search, sorting, debouncing, pagination |
| 4 | 16–20 | Component communication, effects, routes |
| 5 | 21–25 | Reusable components and interactive UI |
| 6 | 26–30 | Custom hooks, context, async logic, tables |

## Final Challenge

Choose any three questions and implement them from scratch in a fresh Vite React project.

```bash
npm create vite@latest react-machine-test -- --template react
cd react-machine-test
npm install
npm run dev
```

Try to complete each question within 20–30 minutes. Test normal input, empty input, repeated clicks, and error conditions before considering the solution complete.

**Recommended order for interview preparation:** Q1, Q2, Q4, Q5, Q7, Q9, Q10, Q11, Q13, Q14, Q15, Q17, Q20, Q26, and Q30.

---

**End of document**
