# Node.js, Express & MongoDB Atlas Backend for React

A clean, minimal backend setup using **Express** for API routing, **Mongoose** to interact with **MongoDB Atlas** (Cloud Database), and **CORS** so your React frontend can communicate with the server seamlessly during development.

---

## 1. Setting Up MongoDB Atlas (Cloud Database)

Before writing your backend code, set up your free cloud database on MongoDB Atlas:

1. **Create an Account & Cluster:**
   * Go to [MongoDB Atlas](https://www.mongodb.com/cloud/atlas) and sign in or create a free account.
   * Click **+ Create** (or **Build a Database**) and select the **M0 Free** tier.
   * Choose a cloud provider (AWS, Google Cloud, or Azure) and a region closest to you, then click **Create Deployment**.

2. **Create a Database User:**
   * Go to **Database Access** (under Security in the left sidebar) and click **Add New Database User**.
   * Choose **Password** authentication.
   * Enter a **Username** (e.g., `myuser`) and a strong **Password** (save this password—you will need it for your `.env` file). *Avoid special characters like `@`, `:`, or `/` in the password, or you will need to URL-encode them later.*
   * Ensure user privileges are set to **Read and write to any database**, then click **Add User**.

3. **Whitelist Your IP Address:**
   * Go to **Network Access** (under Security in the left sidebar) and click **Add IP Address**.
   * Click **Add Current IP Address** (or select **Allow Access from Anywhere** `0.0.0.0/0` for easy development testing) and click **Confirm**.

4. **Get Your Connection String:**
   * Go back to **Database** (in the left sidebar) and click **Connect** next to your cluster.
   * Select **Drivers** (under *Connect to your application*).
   * Select **Node.js** as the driver and copy the connection string provided. It will look like this:
     ```text
     mongodb+srv://myuser:<db_password>@cluster0.abcde.mongodb.net/?retryWrites=true&w=majority&appName=Cluster0
     ```

---

## 2. Project Setup & Dependencies

Open your terminal, initialize a new Node.js project, and install the required packages:

```bash
mkdir backend
cd backend
npm init -y
npm install express mongoose cors dotenv
npm install --save-dev nodemon
```

### Package Overview:
* **`express`**: Web framework to handle API routes (`GET`, `POST`, `PUT`, `DELETE`).
* **`mongoose`**: Object Data Modeling (ODM) library to define schemas and query MongoDB Atlas easily.
* **`cors`**: Middleware to allow cross-origin requests from your React frontend (e.g., React on port `3000` or `5173` talking to Node on port `5000`).
* **`dotenv`**: Loads environment variables (like your Atlas connection string) from a `.env` file.
* **`nodemon`**: Automatically restarts the server whenever you save changes to your files.

---

## 3. Environment Configuration (`.env`)

Create a file named `.env` in the root of your `backend` folder. Paste your MongoDB Atlas connection string here:

```env
PORT=5000
MONGO_URI=mongodb+srv://myuser:your_actual_password@cluster0.abcde.mongodb.net/my_react_app?retryWrites=true&w=majority
```

> **Important Checklist for `MONGO_URI`:**
> 1. Replace `myuser` with your Atlas database username.
> 2. Replace `your_actual_password` with the password you created (remove the `<` and `>` brackets).
> 3. Insert your desired database name (e.g., `my_react_app`) right after `.mongodb.net/` and before the `?`. MongoDB Atlas will automatically create this database the first time you save data!
> 4. Add `.env` to your `.gitignore` file so you never push your database credentials to GitHub.

---

## 4. Complete Server Code (`server.js`)

Create a file named `server.js` in the root of your `backend` folder. This file sets up the middleware, connects to MongoDB Atlas, defines a database schema, and exposes `GET` and `POST` endpoints for your React app:

```javascript
require('dotenv').config();
const express = require('express');
const mongoose = require('mongoose');
const cors = require('cors');

const app = express();

// 1. Middleware
app.use(cors()); // Allows requests from your React frontend
app.use(express.json()); // Parses incoming JSON payloads from React

// 2. Connect to MongoDB Atlas
mongoose.connect(process.env.MONGO_URI)
  .then(() => console.log('✅ Connected to MongoDB Atlas'))
  .catch((err) => console.error('❌ MongoDB Atlas connection error:', err));

// 3. Define a Mongoose Schema & Model
const itemSchema = new mongoose.Schema({
  name: { type: String, required: true },
  description: { type: String },
  createdAt: { type: Date, default: Date.now }
});

const Item = mongoose.model('Item', itemSchema);

// 4. API Routes

// GET: Fetch all items for the React frontend
app.get('/api/items', async (req, res) => {
  try {
    const items = await Item.find().sort({ createdAt: -1 });
    res.status(200).json(items);
  } catch (error) {
    res.status(500).json({ message: 'Error fetching items', error: error.message });
  }
});

// POST: Save new data sent from the React frontend
app.post('/api/items', async (req, res) => {
  try {
    const { name, description } = req.body;
    const newItem = new Item({ name, description });
    const savedItem = await newItem.save();
    res.status(201).json(savedItem);
  } catch (error) {
    res.status(400).json({ message: 'Error saving item', error: error.message });
  }
});

// 5. Start the Server
const PORT = process.env.PORT || 5000;
app.listen(PORT, () => {
  console.log(`🚀 Server running on http://localhost:${PORT}`);
});
```

---

## 5. Running the Backend Server

Start the server with `nodemon` so it automatically reloads when you make changes:

```bash
npx nodemon server.js
```

If everything is configured properly, you will see:
```text
🚀 Server running on http://localhost:5000
✅ Connected to MongoDB Atlas
```

*(Optional)* Add start scripts to your `package.json` file for convenience:

```json
"scripts": {
  "start": "node server.js",
  "dev": "nodemon server.js"
}
```
Then you can simply run `npm run dev`.

---

## 6. Connecting from React (`App.jsx` Example)

In your React frontend project, you can send data to this server and retrieve it from MongoDB Atlas using the native `fetch` API:

```jsx
import { useState, useEffect } from 'react';

function App() {
  const [items, setItems] = useState([]);
  const [name, setName] = useState('');
  const [description, setDescription] = useState('');

  const API_URL = 'http://localhost:5000/api/items';

  // Fetch existing items from MongoDB Atlas when component loads
  useEffect(() => {
    fetch(API_URL)
      .then((res) => res.json())
      .then((data) => setItems(data))
      .catch((err) => console.error('Error fetching data:', err));
  }, []);

  // Submit a new item to Node/MongoDB Atlas
  const handleSubmit = async (e) => {
    e.preventDefault();
    try {
      const response = await fetch(API_URL, {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify({ name, description }),
      });

      if (response.ok) {
        const newItem = await response.json();
        setItems([newItem, ...items]); // Update UI state immediately
        setName('');
        setDescription('');
      }
    } catch (err) {
      console.error('Failed to save item:', err);
    }
  };

  return (
    <div style={{ padding: '20px', fontFamily: 'sans-serif' }}>
      <h2>Add Item to MongoDB Atlas</h2>
      <form onSubmit={handleSubmit} style={{ marginBottom: '20px' }}>
        <input 
          value={name} 
          onChange={(e) => setName(e.target.value)} 
          placeholder="Item Name" 
          required 
          style={{ marginRight: '10px', padding: '8px' }}
        />
        <input 
          value={description} 
          onChange={(e) => setDescription(e.target.value)} 
          placeholder="Description" 
          style={{ marginRight: '10px', padding: '8px' }}
        />
        <button type="submit" style={{ padding: '8px 16px' }}>
          Save to Atlas
        </button>
      </form>

      <h3>Saved Items</h3>
      <ul>
        {items.map((item) => (
          <li key={item._id}>
            <strong>{item.name}</strong>: {item.description}
          </li>
        ))}
      </ul>
    </div>
  );
}

export default App;
```
