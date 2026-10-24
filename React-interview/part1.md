# React Machine Test: 10 Scenario-Based Questions with Answers

**Level:** Beginner to Intermediate\
**Technology:** React + JavaScript\
**Suggested duration:** 90--120 minutes\
**Focus:** State, props, hooks, forms, lists, API calls, Context API,
and routing

## Instructions

Read each scenario, identify the React concept involved, and implement
the requested feature. Reference answers are included after each task.

------------------------------------------------------------------------

## Question 1: Student Attendance Counter

**Core concepts:** `useState`, event handling, functional state updates

### Scenario

A college needs an attendance counter. Show the number of students
present and provide Add, Remove, and Reset buttons. The count must never
become negative.

### Task

Build a component that implements all three operations.

### Answer

``` jsx
import { useState } from "react";

function AttendanceCounter() {
  const [count, setCount] = useState(0);

  return (
    <div>
      <h2>Students Present: {count}</h2>

      <button onClick={() => setCount(c => c + 1)}>
        Add Student
      </button>

      <button onClick={() => setCount(c => Math.max(0, c - 1))}>
        Remove Student
      </button>

      <button onClick={() => setCount(0)}>
        Reset
      </button>
    </div>
  );
}

export default AttendanceCounter;
```

**Key learning:** `useState()` stores component state. Calling its
setter triggers a re-render. Functional updates are useful when the next
value depends on the previous value.

------------------------------------------------------------------------

## Question 2: Live Search for Students

**Core concepts:** Controlled inputs, `filter()`, `map()`, list keys

### Scenario

A teacher has a list of students and wants to search by name. The list
should update as the teacher types, ignoring letter case.

### Task

Create a search field that filters the original list without modifying
it.

### Answer

``` jsx
import { useState } from "react";

const students = ["Abin", "Anu", "Rahul", "Arun", "Meera"];

function StudentSearch() {
  const [search, setSearch] = useState("");

  const filteredStudents = students.filter(student =>
    student.toLowerCase().includes(search.toLowerCase())
  );

  return (
    <div>
      <h2>Student Directory</h2>

      <input
        value={search}
        onChange={e => setSearch(e.target.value)}
        placeholder="Search student..."
      />

      {filteredStudents.length > 0 ? (
        <ul>
          {filteredStudents.map(student => (
            <li key={student}>{student}</li>
          ))}
        </ul>
      ) : (
        <p>No students found.</p>
      )}
    </div>
  );
}

export default StudentSearch;
```

**Key learning:** A controlled input gets its value from React state.
`filter()` creates a matching list, and `map()` renders it. Use stable,
unique keys when rendering lists.

------------------------------------------------------------------------

## Question 3: Reusable Student Profile Card

**Core concepts:** Functional components, props, destructuring

### Scenario

A college portal displays many student profile cards. Every card shares
the same layout but has a different name, department, and semester.

### Task

Create a reusable `StudentCard` component and render three examples.

### Answer

``` jsx
function StudentCard({ name, department, semester }) {
  return (
    <div className="student-card">
      <h3>{name}</h3>
      <p>Department: {department}</p>
      <p>Semester: {semester}</p>
    </div>
  );
}

function App() {
  return (
    <div>
      <StudentCard name="Abin" department="MCA" semester={9} />
      <StudentCard name="Anu" department="CSE" semester={5} />
      <StudentCard name="Rahul" department="ECE" semester={3} />
    </div>
  );
}

export default App;
```

**Key learning:** Props allow a parent component to pass data to a child
component. Reusable components reduce duplication.

------------------------------------------------------------------------

## Question 4: Fetch Products from an API

**Core concepts:** `useEffect`, `fetch()`, async/await, error handling

### Scenario

An online shop must fetch products when the component first loads.
Display a loading message while waiting and an error message if the
request fails.

### Task

Fetch from `https://fakestoreapi.com/products` and display product
titles and prices.

### Answer

``` jsx
import { useState, useEffect } from "react";

function Products() {
  const [products, setProducts] = useState([]);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState("");

  useEffect(() => {
    async function fetchProducts() {
      try {
        const response = await fetch(
          "https://fakestoreapi.com/products"
        );

        if (!response.ok) {
          throw new Error("Failed to fetch products");
        }

        const data = await response.json();
        setProducts(data);
      } catch (err) {
        setError(err.message);
      } finally {
        setLoading(false);
      }
    }

    fetchProducts();
  }, []);

  if (loading) return <p>Loading products...</p>;
  if (error) return <p>{error}</p>;

  return (
    <div>
      <h2>Products</h2>
      {products.map(product => (
        <div key={product.id}>
          <h3>{product.title}</h3>
          <p>${product.price}</p>
        </div>
      ))}
    </div>
  );
}

export default Products;
```

**Key learning:** The empty dependency array runs this effect after the
initial mount. The example handles loading, errors, and successful
results. Production code may also need request cancellation if
dependencies can change or the component unmounts.

------------------------------------------------------------------------

## Question 5: To-Do List with Add and Delete

**Core concepts:** Forms, state, immutable array updates, `filter()`

### Scenario

A student needs a task manager where they can add tasks and delete
individual tasks. Blank or whitespace-only tasks must not be accepted.

### Task

Implement task creation and deletion.

### Answer

``` jsx
import { useState } from "react";

function TodoApp() {
  const [task, setTask] = useState("");
  const [tasks, setTasks] = useState([]);

  function addTask(e) {
    e.preventDefault();

    const value = task.trim();
    if (!value) return;

    setTasks(prev => [
      ...prev,
      { id: crypto.randomUUID(), text: value }
    ]);

    setTask("");
  }

  function deleteTask(id) {
    setTasks(prev => prev.filter(item => item.id !== id));
  }

  return (
    <div>
      <h2>My Tasks</h2>

      <form onSubmit={addTask}>
        <input
          value={task}
          onChange={e => setTask(e.target.value)}
          placeholder="Enter a task"
        />
        <button type="submit">Add</button>
      </form>

      {tasks.map(item => (
        <div key={item.id}>
          {item.text}
          <button onClick={() => deleteTask(item.id)}>
            Delete
          </button>
        </div>
      ))}
    </div>
  );
}

export default TodoApp;
```

**Key learning:** `preventDefault()` prevents the form's normal page
submission. Spread syntax and `filter()` create new arrays instead of
mutating state. `crypto.randomUUID()` works in modern secure browser
contexts.

------------------------------------------------------------------------

## Question 6: Show and Hide Password

**Core concepts:** Boolean state, conditional rendering

### Scenario

A login form has a password field. Users should be able to toggle
between a masked password and visible text.

### Task

Implement a Show/Hide button.

### Answer

``` jsx
import { useState } from "react";

function PasswordField() {
  const [visible, setVisible] = useState(false);

  return (
    <div>
      <label htmlFor="password">Password: </label>

      <input
        id="password"
        type={visible ? "text" : "password"}
        placeholder="Enter password"
      />

      <button
        type="button"
        onClick={() => setVisible(prev => !prev)}
      >
        {visible ? "Hide" : "Show"}
      </button>
    </div>
  );
}

export default PasswordField;
```

**Key learning:** The input type and button text depend on boolean
state. `type="button"` prevents the button from submitting a surrounding
form.

------------------------------------------------------------------------

## Question 7: Shopping Cart Total

**Core concepts:** Array state, `reduce()`, derived values

### Scenario

An online store lists products. Clicking Add to Cart adds one unit. Show
the total number of cart items and the combined price without storing
redundant totals in state.

### Task

Build the product list and cart summary.

### Answer

``` jsx
import { useState } from "react";

const products = [
  { id: 1, name: "Keyboard", price: 800 },
  { id: 2, name: "Mouse", price: 400 },
  { id: 3, name: "Headphones", price: 1200 }
];

function Shop() {
  const [cart, setCart] = useState([]);

  function addToCart(product) {
    setCart(prev => [...prev, product]);
  }

  const totalItems = cart.length;

  const totalPrice = cart.reduce(
    (sum, product) => sum + product.price,
    0
  );

  return (
    <div>
      <h2>Products</h2>

      {products.map(product => (
        <div key={product.id}>
          {product.name} - Rs. {product.price}
          <button onClick={() => addToCart(product)}>
            Add to Cart
          </button>
        </div>
      ))}

      <h3>Cart Items: {totalItems}</h3>
      <h3>Total: Rs. {totalPrice}</h3>
    </div>
  );
}

export default Shop;
```

**Key learning:** Derived values are calculated from the current cart
state. This avoids synchronization bugs caused by storing the same
information in multiple state variables. This simplified cart adds one
item per click.

------------------------------------------------------------------------

## Question 8: Dark Mode Across Components

**Core concepts:** `createContext()`, `useContext()`, shared state

### Scenario

A dashboard has a navbar and content area. Selecting dark mode in the
navbar should update both areas without passing props through every
intermediate component.

### Task

Create a theme context and provider.

### Answer

``` jsx
import {
  createContext,
  useContext,
  useState
} from "react";

const ThemeContext = createContext(null);

function ThemeProvider({ children }) {
  const [dark, setDark] = useState(false);

  return (
    <ThemeContext.Provider value={{ dark, setDark }}>
      {children}
    </ThemeContext.Provider>
  );
}

function Navbar() {
  const { dark, setDark } = useContext(ThemeContext);

  return (
    <nav>
      <button onClick={() => setDark(prev => !prev)}>
        {dark ? "Light Mode" : "Dark Mode"}
      </button>
    </nav>
  );
}

function Content() {
  const { dark } = useContext(ThemeContext);

  return (
    <main
      style={{
        background: dark ? "#222" : "#fff",
        color: dark ? "#fff" : "#222",
        padding: "20px"
      }}
    >
      <h2>College Dashboard</h2>
      <p>Theme changes are shared across components.</p>
    </main>
  );
}

function App() {
  return (
    <ThemeProvider>
      <Navbar />
      <Content />
    </ThemeProvider>
  );
}

export default App;
```

**Key learning:** Context helps share values across a component tree
without manually passing props through each level. In larger
applications, consider memoizing context values if unnecessary
re-renders become a problem.

------------------------------------------------------------------------

## Question 9: Filter Students by Department

**Core concepts:** Controlled select, `filter()`, conditional logic

### Scenario

An administrator manages MCA, CSE, and ECE students. A dropdown should
filter the list by department, while All Departments shows everyone.

### Task

Implement department filtering.

### Answer

``` jsx
import { useState } from "react";

const students = [
  { id: 1, name: "Abin", department: "MCA" },
  { id: 2, name: "Anu", department: "CSE" },
  { id: 3, name: "Rahul", department: "ECE" },
  { id: 4, name: "Meera", department: "MCA" }
];

function DepartmentFilter() {
  const [department, setDepartment] = useState("All");

  const filtered = students.filter(student =>
    department === "All" ||
    student.department === department
  );

  return (
    <div>
      <h2>Student Management</h2>

      <select
        value={department}
        onChange={e => setDepartment(e.target.value)}
      >
        <option value="All">All Departments</option>
        <option value="MCA">MCA</option>
        <option value="CSE">CSE</option>
        <option value="ECE">ECE</option>
      </select>

      <ul>
        {filtered.map(student => (
          <li key={student.id}>
            {student.name} — {student.department}
          </li>
        ))}
      </ul>
    </div>
  );
}

export default DepartmentFilter;
```

**Key learning:** A controlled select gets its value from React state.
The displayed list is derived from the selected department.

------------------------------------------------------------------------

## Question 10: Navigate Between Home and Profile Without Refreshing

**Core concepts:** React Router, single-page applications (SPA), routes

### Scenario

A student portal has Home and Profile pages. Clicking a link should
switch pages without a full browser reload. Direct visits to `/profile`
should work when the server supports SPA fallback routing.

### Task

Create Home, Profile, and 404 routes.

Install React Router:

``` bash
npm install react-router-dom
```

### Answer --- `App.jsx`

``` jsx
import {
  BrowserRouter,
  Routes,
  Route,
  Link
} from "react-router-dom";

function Home() {
  return <h2>Welcome to Student Portal</h2>;
}

function Profile() {
  return <h2>Student Profile</h2>;
}

function NotFound() {
  return <h2>404 - Page Not Found</h2>;
}

function App() {
  return (
    <BrowserRouter>
      <nav>
        <Link to="/">Home</Link>{" | "}
        <Link to="/profile">Profile</Link>
      </nav>

      <Routes>
        <Route path="/" element={<Home />} />
        <Route path="/profile" element={<Profile />} />
        <Route path="*" element={<NotFound />} />
      </Routes>
    </BrowserRouter>
  );
}

export default App;
```

**Key learning:** React Router handles client-side navigation. Configure
the deployment server to return `index.html` for valid app routes so
direct URL access and browser refresh work.

------------------------------------------------------------------------

## Quick Revision Sheet

  Question   Main concept            Key API or method
  ---------- ----------------------- --------------------------------
  1          State                   `useState`
  2          Live search             `filter()`, `map()`
  3          Reusable components     Props
  4          API calls               `useEffect`, `fetch()`
  5          To-do list              Forms, immutable array updates
  6          Conditional rendering   Boolean state
  7          Shopping cart           `reduce()`, derived values
  8          Shared theme            Context API, `useContext`
  9          Department filtering    Controlled select, `filter()`
  10         Page navigation         React Router

## Suggested Evaluation Rubric

  ------------------------------------------------------------------------
  Criteria                                    Weight What to assess
  --------------------- ---------------------------- ---------------------
  Correct functionality                          40% Required features
                                                     work as described

  React concepts                                 25% Correct use of hooks,
                                                     props, state, and
                                                     routing

  Code quality                                   20% Readable,
                                                     maintainable
                                                     implementation

  UI and edge cases                              15% Responsive layout,
                                                     empty states, and
                                                     error handling
  ------------------------------------------------------------------------

## Machine Test Tips

1.  Implement the required functionality first.
2.  Test empty inputs, invalid actions, and API failures.
3.  Use stable keys when rendering lists.
4.  Avoid directly mutating React state.
5.  Be ready to explain why each hook is used and how state updates
    trigger rendering.
6.  Improve the UI after the core requirements work.
