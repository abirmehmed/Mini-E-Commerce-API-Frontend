# Mini-E-Commerce-API-Frontend

Here’s a **6-day guide** to help you build the Mini E-Commerce API + Frontend project. This plan assumes you have some familiarity with Node.js, MySQL, React/Next.js, and Tailwind CSS. Each day is structured to ensure steady progress.

---

# **Day 1: Backend Setup & Database Design**
**Objective**: Set up the backend, design the database, and create REST API endpoints.

1. **Project Setup**:
   - Initialize a Node.js project (`npm init -y`).
   - Install dependencies: `express`, `mysql2`, `dotenv`, `jsonwebtoken`, `cors`, `bcrypt`, `redis` (optional for cart).
   - Set up an Express server with basic middleware (e.g., `express.json()`).

2. **Database Design**:
   - Create a MySQL database (`ecommerce_db`).
   - Design and create tables:
     ```sql
     CREATE TABLE Users (
         id INT AUTO_INCREMENT PRIMARY KEY,
         username VARCHAR(50) UNIQUE NOT NULL,
         password VARCHAR(255) NOT NULL,
         role ENUM('admin', 'customer') DEFAULT 'customer'
     );

     CREATE TABLE Categories (
         id INT AUTO_INCREMENT PRIMARY KEY,
         name VARCHAR(50) NOT NULL
     );

     CREATE TABLE Products (
         id INT AUTO_INCREMENT PRIMARY KEY,
         name VARCHAR(100) NOT NULL,
         description TEXT,
         price DECIMAL(10, 2) NOT NULL,
         category_id INT,
         rating DECIMAL(3, 2) DEFAULT 0,
         stock INT DEFAULT 0,
         FOREIGN KEY (category_id) REFERENCES Categories(id)
     );

     CREATE TABLE Customers (
         id INT AUTO_INCREMENT PRIMARY KEY,
         user_id INT UNIQUE,
         name VARCHAR(100) NOT NULL,
         email VARCHAR(100) UNIQUE NOT NULL,
         address TEXT,
         FOREIGN KEY (user_id) REFERENCES Users(id)
     );

     CREATE TABLE Orders (
         id INT AUTO_INCREMENT PRIMARY KEY,
         customer_id INT,
         total_amount DECIMAL(10, 2) NOT NULL,
         status ENUM('pending', 'completed', 'cancelled') DEFAULT 'pending',
         created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
         FOREIGN KEY (customer_id) REFERENCES Customers(id)
     );

     CREATE TABLE OrderItems (
         id INT AUTO_INCREMENT PRIMARY KEY,
         order_id INT,
         product_id INT,
         quantity INT NOT NULL,
         price DECIMAL(10, 2) NOT NULL,
         FOREIGN KEY (order_id) REFERENCES Orders(id),
         FOREIGN KEY (product_id) REFERENCES Products(id)
     );
     ```

3. **REST API Endpoints**:
   - Implement basic CRUD endpoints for:
     - `GET /products` (list products with filters).
     - `GET /categories` (list categories).
     - `POST /cart` (add to cart using session or Redis).
     - `POST /checkout` (place an order).
     - `GET /customers` (retrieve customer info).
     - `POST /customers` (add new customer).

4. **Authentication**:
   - Implement JWT-based authentication for users (e.g., `/login` and `/register` endpoints).

---

### **Day 2: Backend Logic & Optimization**
**Objective**: Implement backend logic, validation, and optimize database queries.

1. **Product Filtering**:
   - Add filtering logic for `/products` endpoint:
     - Filter by category, price range, rating, and search query.
     - Use SQL `WHERE` clauses and indexing for optimization.

2. **Cart Functionality**:
   - Implement cart logic using session storage or Redis.
   - Store cart items temporarily and associate them with the user.

3. **Order Validation**:
   - Validate stock availability before placing an order.
   - Prevent duplicate orders using unique constraints or checks.

4. **Database Indexing**:
   - Add indexes to frequently queried columns (e.g., `Products.name`, `Products.price`, `Products.category_id`).

5. **Error Handling**:
   - Add middleware for error handling (e.g., 404, 500 errors).

---

### **Day 3: Frontend Setup & Basic Pages**
**Objective**: Set up the frontend and create basic pages.

1. **Project Setup**:
   - Initialize a Next.js project (`npx create-next-app`).
   - Install Tailwind CSS for styling.
   - Set up a basic layout with a navbar.

2. **Pages**:
   - Create the following pages:
     - `Home Page`: Display a list of products.
     - `Product Details Page`: Show detailed product information.
     - `Cart Page`: Display added products.
     - `Checkout Page`: Display order summary and checkout button.
     - `Customer Page`: Display customer details and order history.

3. **API Integration**:
   - Fetch products and categories from the backend API using `fetch` or `axios`.

4. **State Management**:
   - Set up a Cart context using React Context API or Redux.

---

### **Day 4: Frontend Functionality**
**Objective**: Implement frontend functionality for browsing, filtering, and cart management.

1. **Product Listing**:
   - Display products on the home page with a grid layout.
   - Add search, category filter, price range filter, and rating filter.

2. **Product Details**:
   - Show detailed product information on the product details page.

3. **Cart Management**:
   - Add products to the cart and update the cart state.
   - Display cart items on the cart page with options to remove or update quantity.

4. **Checkout Page**:
   - Display order summary and a checkout button.

---

### **Day 5: Checkout & Order Processing**
**Objective**: Implement checkout functionality and order processing.

1. **Checkout Flow**:
   - Allow users to enter customer details during checkout.
   - Validate customer details and cart items before placing an order.

2. **Order Placement**:
   - Send a `POST /checkout` request to the backend to place the order.
   - Update the database with the order and order items.

3. **Customer Page**:
   - Display customer details and order history on the customer page.

4. **Error Handling**:
   - Add error handling for API requests (e.g., display error messages).

---

### **Day 6: Final Enhancements & Deployment**
**Objective**: Add final touches, optimize, and deploy the project.

1. **Loading States**:
   - Add loading states for API requests.

2. **Pagination**:
   - Implement pagination for the product list.

3. **Deployment**:
   - Deploy the backend on Render or Vercel.
   - Deploy the frontend on Vercel or Netlify.

4. **Testing**:
   - Test the entire flow (browsing, filtering, cart, checkout, order history).

5. **(Optional) Rating System**:
   - Allow customers to rate products after placing an order.

---

### **Tools & Resources**
- **Backend**: Node.js, Express.js, MySQL, JWT, Redis (optional).
- **Frontend**: Next.js, React, Tailwind CSS, Context API/Redux.
- **Deployment**: Render, Vercel, Netlify.
- **Database**: MySQL (local or cloud-based like AWS RDS or PlanetScale).


---

Absolutely! Let’s break **Day 1** into a **step-by-step process** so you can build the backend and database for your Mini E-Commerce project. I’ll keep it simple and actionable.

---

# **Day 1: Backend Setup & Database Design**
**Goal**: Set up the backend, design the database, and create REST API endpoints.

---

### **Step 1: Initialize the Project**
1. Create a new folder for your project:
   ```bash
   mkdir mini-ecommerce
   cd mini-ecommerce
   ```
2. Initialize a Node.js project:
   ```bash
   npm init -y
   ```
3. Install required dependencies:
   ```bash
   npm install express mysql2 dotenv jsonwebtoken bcrypt cors
   ```
   - `express`: For building the backend server.
   - `mysql2`: For connecting to MySQL.
   - `dotenv`: For managing environment variables.
   - `jsonwebtoken`: For JWT-based authentication.
   - `bcrypt`: For hashing passwords.
   - `cors`: For enabling Cross-Origin Resource Sharing (frontend-backend communication).

4. Create a `.env` file to store sensitive information:
   ```env
   PORT=5000
   DB_HOST=localhost
   DB_USER=root
   DB_PASSWORD=your_mysql_password
   DB_NAME=ecommerce_db
   JWT_SECRET=your_jwt_secret_key
   ```

---

### **Step 2: Set Up the Express Server**
1. Create an `index.js` file in the root directory:
   ```javascript
   const express = require('express');
   const mysql = require('mysql2');
   const dotenv = require('dotenv');
   const cors = require('cors');

   dotenv.config();

   const app = express();
   const PORT = process.env.PORT || 5000;

   // Middleware
   app.use(cors());
   app.use(express.json());

   // Database connection
   const db = mysql.createConnection({
     host: process.env.DB_HOST,
     user: process.env.DB_USER,
     password: process.env.DB_PASSWORD,
     database: process.env.DB_NAME,
   });

   db.connect((err) => {
     if (err) {
       console.error('Error connecting to MySQL:', err);
       return;
     }
     console.log('Connected to MySQL database');
   });

   // Test route
   app.get('/', (req, res) => {
     res.send('Mini E-Commerce Backend is running!');
   });

   // Start server
   app.listen(PORT, () => {
     console.log(`Server is running on http://localhost:${PORT}`);
   });
   ```

2. Run the server:
   ```bash
   node index.js
   ```
   - Open your browser and go to `http://localhost:5000`. You should see: `Mini E-Commerce Backend is running!`.

---

### **Step 3: Design the Database**
1. Open your MySQL terminal or use a tool like phpMyAdmin or MySQL Workbench.
2. Create the database:
   ```sql
   CREATE DATABASE ecommerce_db;
   USE ecommerce_db;
   ```
3. Create the tables:
   ```sql
   CREATE TABLE Users (
       id INT AUTO_INCREMENT PRIMARY KEY,
       username VARCHAR(50) UNIQUE NOT NULL,
       password VARCHAR(255) NOT NULL,
       role ENUM('admin', 'customer') DEFAULT 'customer'
   );

   CREATE TABLE Categories (
       id INT AUTO_INCREMENT PRIMARY KEY,
       name VARCHAR(50) NOT NULL
   );

   CREATE TABLE Products (
       id INT AUTO_INCREMENT PRIMARY KEY,
       name VARCHAR(100) NOT NULL,
       description TEXT,
       price DECIMAL(10, 2) NOT NULL,
       category_id INT,
       rating DECIMAL(3, 2) DEFAULT 0,
       stock INT DEFAULT 0,
       FOREIGN KEY (category_id) REFERENCES Categories(id)
   );

   CREATE TABLE Customers (
       id INT AUTO_INCREMENT PRIMARY KEY,
       user_id INT UNIQUE,
       name VARCHAR(100) NOT NULL,
       email VARCHAR(100) UNIQUE NOT NULL,
       address TEXT,
       FOREIGN KEY (user_id) REFERENCES Users(id)
   );

   CREATE TABLE Orders (
       id INT AUTO_INCREMENT PRIMARY KEY,
       customer_id INT,
       total_amount DECIMAL(10, 2) NOT NULL,
       status ENUM('pending', 'completed', 'cancelled') DEFAULT 'pending',
       created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
       FOREIGN KEY (customer_id) REFERENCES Customers(id)
   );

   CREATE TABLE OrderItems (
       id INT AUTO_INCREMENT PRIMARY KEY,
       order_id INT,
       product_id INT,
       quantity INT NOT NULL,
       price DECIMAL(10, 2) NOT NULL,
       FOREIGN KEY (order_id) REFERENCES Orders(id),
       FOREIGN KEY (product_id) REFERENCES Products(id)
   );
   ```

---

### **Step 4: Create REST API Endpoints**
1. Add the following routes to `index.js`:
   ```javascript
   // Get all products
   app.get('/products', (req, res) => {
     const query = 'SELECT * FROM Products';
     db.query(query, (err, results) => {
       if (err) throw err;
       res.json(results);
     });
   });

   // Get all categories
   app.get('/categories', (req, res) => {
     const query = 'SELECT * FROM Categories';
     db.query(query, (err, results) => {
       if (err) throw err;
       res.json(results);
     });
   });

   // Add a product to cart (placeholder for now)
   app.post('/cart', (req, res) => {
     res.json({ message: 'Product added to cart' });
   });

   // Place an order (placeholder for now)
   app.post('/checkout', (req, res) => {
     res.json({ message: 'Order placed successfully' });
   });

   // Get customer details
   app.get('/customers', (req, res) => {
     const query = 'SELECT * FROM Customers';
     db.query(query, (err, results) => {
       if (err) throw err;
       res.json(results);
     });
   });

   // Add a new customer
   app.post('/customers', (req, res) => {
     const { user_id, name, email, address } = req.body;
     const query = 'INSERT INTO Customers (user_id, name, email, address) VALUES (?, ?, ?, ?)';
     db.query(query, [user_id, name, email, address], (err, results) => {
       if (err) throw err;
       res.json({ message: 'Customer added successfully' });
     });
   });
   ```

---

### **Step 5: Test the Endpoints**
1. Use a tool like **Postman** or **Thunder Client (VS Code extension)** to test the endpoints:
   - `GET /products`
   - `GET /categories`
   - `POST /customers`
   - `GET /customers`

2. Insert some sample data into the database:
   ```sql
   INSERT INTO Categories (name) VALUES ('Electronics'), ('Clothing'), ('Books');
   INSERT INTO Products (name, description, price, category_id, stock) VALUES
   ('Laptop', 'High-performance laptop', 999.99, 1, 10),
   ('T-Shirt', 'Cotton T-Shirt', 19.99, 2, 50),
   ('JavaScript Book', 'Learn JavaScript in 30 days', 29.99, 3, 20);
   ```

---

### **Step 6: Wrap Up**
- Commit your code to GitHub:
  ```bash
  git init
  git add .
  git commit -m "Day 1: Backend setup and database design"
  git branch -M main
  git remote add origin <your-repo-url>
  git push -u origin main
  ```

---


---

# **Day 2: Backend Logic & Optimization**
**Goal**: Implement product filtering, cart functionality, order validation, and optimize database queries.

---

### **Step 1: Implement Product Filtering**
1. Update the `GET /products` endpoint to support filtering by category, price, rating, and search query.
   ```javascript
   app.get('/products', (req, res) => {
     const { category, minPrice, maxPrice, minRating, search } = req.query;
     let query = 'SELECT * FROM Products WHERE 1=1';
     const params = [];

     if (category) {
       query += ' AND category_id = ?';
       params.push(category);
     }
     if (minPrice) {
       query += ' AND price >= ?';
       params.push(minPrice);
     }
     if (maxPrice) {
       query += ' AND price <= ?';
       params.push(maxPrice);
     }
     if (minRating) {
       query += ' AND rating >= ?';
       params.push(minRating);
     }
     if (search) {
       query += ' AND name LIKE ?';
       params.push(`%${search}%`);
     }

     db.query(query, params, (err, results) => {
       if (err) throw err;
       res.json(results);
     });
   });
   ```

2. Test the endpoint with different filters using Postman or Thunder Client:
   - `GET /products?category=1` (filter by category).
   - `GET /products?minPrice=10&maxPrice=100` (filter by price range).
   - `GET /products?minRating=4` (filter by rating).
   - `GET /products?search=laptop` (search by product name).

---

### **Step 2: Implement Cart Functionality**
1. Use in-memory storage (or Redis) to manage the cart. For simplicity, we’ll use a JavaScript object.
   ```javascript
   let cart = {};

   // Add to cart
   app.post('/cart', (req, res) => {
     const { productId, quantity } = req.body;
     if (!cart[productId]) {
       cart[productId] = 0;
     }
     cart[productId] += quantity;
     res.json({ message: 'Product added to cart', cart });
   });

   // Get cart
   app.get('/cart', (req, res) => {
     res.json({ cart });
   });

   // Clear cart
   app.delete('/cart', (req, res) => {
     cart = {};
     res.json({ message: 'Cart cleared' });
   });
   ```

2. Test the cart endpoints:
   - `POST /cart` with body `{ "productId": 1, "quantity": 2 }`.
   - `GET /cart` to see the cart contents.
   - `DELETE /cart` to clear the cart.

---

### **Step 3: Implement Order Validation**
1. Add a `POST /checkout` endpoint to place an order.
   ```javascript
   app.post('/checkout', (req, res) => {
     const { customerId, items } = req.body;

     // Validate stock availability
     let valid = true;
     for (const item of items) {
       const query = 'SELECT stock FROM Products WHERE id = ?';
       db.query(query, [item.productId], (err, results) => {
         if (err) throw err;
         if (results[0].stock < item.quantity) {
           valid = false;
         }
       });
     }

     if (!valid) {
       return res.status(400).json({ message: 'Insufficient stock for some items' });
     }

     // Create order
     const orderQuery = 'INSERT INTO Orders (customer_id, total_amount, status) VALUES (?, ?, ?)';
     const totalAmount = items.reduce((sum, item) => sum + item.price * item.quantity, 0);
     db.query(orderQuery, [customerId, totalAmount, 'pending'], (err, results) => {
       if (err) throw err;
       const orderId = results.insertId;

       // Add order items
       for (const item of items) {
         const orderItemQuery = 'INSERT INTO OrderItems (order_id, product_id, quantity, price) VALUES (?, ?, ?, ?)';
         db.query(orderItemQuery, [orderId, item.productId, item.quantity, item.price], (err) => {
           if (err) throw err;
         });

         // Update product stock
         const updateStockQuery = 'UPDATE Products SET stock = stock - ? WHERE id = ?';
         db.query(updateStockQuery, [item.quantity, item.productId], (err) => {
           if (err) throw err;
         });
       }

       res.json({ message: 'Order placed successfully', orderId });
     });
   });
   ```

2. Test the `POST /checkout` endpoint with a sample request:
   ```json
   {
     "customerId": 1,
     "items": [
       { "productId": 1, "quantity": 2, "price": 999.99 },
       { "productId": 2, "quantity": 1, "price": 19.99 }
     ]
   }
   ```

---

### **Step 4: Optimize Database Queries**
1. Add indexes to frequently queried columns:
   ```sql
   CREATE INDEX idx_products_name ON Products(name);
   CREATE INDEX idx_products_price ON Products(price);
   CREATE INDEX idx_products_category ON Products(category_id);
   ```

2. Test the performance of the `GET /products` endpoint with filters.

---

### **Step 5: Error Handling**
1. Add error handling middleware to `index.js`:
   ```javascript
   app.use((err, req, res, next) => {
     console.error(err.stack);
     res.status(500).json({ message: 'Something went wrong!' });
   });
   ```

2. Test error handling by intentionally causing errors (e.g., invalid SQL queries).

---

### **Step 6: Wrap Up**
1. Commit your code to GitHub:
   ```bash
   git add .
   git commit -m "Day 2: Implemented filtering, cart, order validation, and query optimization"
   git push origin main
   ```

---

Absolutely! Let’s break **Day 3** into a **step-by-step process** so you can set up the frontend and create basic pages for your Mini E-Commerce project. I’ll keep it simple and actionable.

---

# **Day 3: Frontend Setup & Basic Pages**
**Goal**: Set up the frontend using Next.js/React and create basic pages for the e-commerce app.

---

### **Step 1: Initialize the Frontend Project**
1. Create a new Next.js project:
   ```bash
   npx create-next-app@latest frontend
   cd frontend
   ```
2. Install Tailwind CSS for styling:
   ```bash
   npm install -D tailwindcss postcss autoprefixer
   npx tailwindcss init -p
   ```
3. Configure Tailwind CSS:
   - Update `tailwind.config.js`:
     ```javascript
     module.exports = {
       content: [
         './pages/**/*.{js,ts,jsx,tsx}',
         './components/**/*.{js,ts,jsx,tsx}',
       ],
       theme: {
         extend: {},
       },
       plugins: [],
     };
     ```
   - Replace the content of `styles/globals.css` with:
     ```css
     @tailwind base;
     @tailwind components;
     @tailwind utilities;
     ```

4. Start the development server:
   ```bash
   npm run dev
   ```
   - Open your browser and go to `http://localhost:3000`. You should see the default Next.js page.

---

### **Step 2: Create Basic Pages**
1. **Home Page** (`pages/index.js`):
   - Display a list of products with filters.
   ```javascript
   import { useEffect, useState } from 'react';

   export default function Home() {
     const [products, setProducts] = useState([]);
     const [categories, setCategories] = useState([]);
     const [filters, setFilters] = useState({
       category: '',
       minPrice: '',
       maxPrice: '',
       minRating: '',
       search: '',
     });

     useEffect(() => {
       fetchProducts();
       fetchCategories();
     }, []);

     const fetchProducts = async () => {
       const query = new URLSearchParams(filters).toString();
       const res = await fetch(`http://localhost:5000/products?${query}`);
       const data = await res.json();
       setProducts(data);
     };

     const fetchCategories = async () => {
       const res = await fetch('http://localhost:5000/categories');
       const data = await res.json();
       setCategories(data);
     };

     const handleFilterChange = (e) => {
       const { name, value } = e.target;
       setFilters({ ...filters, [name]: value });
     };

     const applyFilters = () => {
       fetchProducts();
     };

     return (
       <div className="container mx-auto p-4">
         <h1 className="text-2xl font-bold mb-4">Products</h1>
         <div className="grid grid-cols-1 md:grid-cols-4 gap-4">
           <div className="md:col-span-1">
             <h2 className="text-xl font-semibold mb-2">Filters</h2>
             <div className="space-y-4">
               <select
                 name="category"
                 value={filters.category}
                 onChange={handleFilterChange}
                 className="w-full p-2 border rounded"
               >
                 <option value="">All Categories</option>
                 {categories.map((cat) => (
                   <option key={cat.id} value={cat.id}>{cat.name}</option>
                 ))}
               </select>
               <input
                 type="number"
                 name="minPrice"
                 placeholder="Min Price"
                 value={filters.minPrice}
                 onChange={handleFilterChange}
                 className="w-full p-2 border rounded"
               />
               <input
                 type="number"
                 name="maxPrice"
                 placeholder="Max Price"
                 value={filters.maxPrice}
                 onChange={handleFilterChange}
                 className="w-full p-2 border rounded"
               />
               <input
                 type="number"
                 name="minRating"
                 placeholder="Min Rating"
                 value={filters.minRating}
                 onChange={handleFilterChange}
                 className="w-full p-2 border rounded"
               />
               <input
                 type="text"
                 name="search"
                 placeholder="Search"
                 value={filters.search}
                 onChange={handleFilterChange}
                 className="w-full p-2 border rounded"
               />
               <button
                 onClick={applyFilters}
                 className="w-full bg-blue-500 text-white p-2 rounded"
               >
                 Apply Filters
               </button>
             </div>
           </div>
           <div className="md:col-span-3">
             <div className="grid grid-cols-1 md:grid-cols-3 gap-4">
               {products.map((product) => (
                 <div key={product.id} className="border p-4 rounded">
                   <h2 className="text-xl font-semibold">{product.name}</h2>
                   <p className="text-gray-600">{product.description}</p>
                   <p className="text-green-600 font-bold">${product.price}</p>
                   <p className="text-yellow-500">Rating: {product.rating}</p>
                 </div>
               ))}
             </div>
           </div>
         </div>
       </div>
     );
   }
   ```

2. **Product Details Page** (`pages/products/[id].js`):
   - Create a dynamic route for product details.
   ```javascript
   import { useRouter } from 'next/router';
   import { useEffect, useState } from 'react';

   export default function ProductDetails() {
     const router = useRouter();
     const { id } = router.query;
     const [product, setProduct] = useState(null);

     useEffect(() => {
       if (id) {
         fetchProduct();
       }
     }, [id]);

     const fetchProduct = async () => {
       const res = await fetch(`http://localhost:5000/products/${id}`);
       const data = await res.json();
       setProduct(data);
     };

     if (!product) return <p>Loading...</p>;

     return (
       <div className="container mx-auto p-4">
         <h1 className="text-2xl font-bold mb-4">{product.name}</h1>
         <p className="text-gray-600">{product.description}</p>
         <p className="text-green-600 font-bold">${product.price}</p>
         <p className="text-yellow-500">Rating: {product.rating}</p>
       </div>
     );
   }
   ```

3. **Cart Page** (`pages/cart.js`):
   - Display the cart contents.
   ```javascript
   import { useEffect, useState } from 'react';

   export default function Cart() {
     const [cart, setCart] = useState({});

     useEffect(() => {
       fetchCart();
     }, []);

     const fetchCart = async () => {
       const res = await fetch('http://localhost:5000/cart');
       const data = await res.json();
       setCart(data.cart);
     };

     return (
       <div className="container mx-auto p-4">
         <h1 className="text-2xl font-bold mb-4">Cart</h1>
         <div className="space-y-4">
           {Object.keys(cart).map((productId) => (
             <div key={productId} className="border p-4 rounded">
               <p>Product ID: {productId}</p>
               <p>Quantity: {cart[productId]}</p>
             </div>
           ))}
         </div>
       </div>
     );
   }
   ```

---

### **Step 3: Wrap Up**
1. Commit your code to GitHub:
   ```bash
   git add .
   git commit -m "Day 3: Frontend setup and basic pages"
   git push origin main
   ```

---


Absolutely! Let’s break **Day 4** into a **step-by-step process** so you can implement frontend functionality for browsing, filtering, and cart management. I’ll keep it simple and actionable.

---

# **Day 4: Frontend Functionality**
**Goal**: Implement product browsing, filtering, cart management, and integrate with the backend.

---

### **Step 1: Enhance the Home Page**
1. **Add "Add to Cart" Button**:
   - Update the product card in `pages/index.js` to include an "Add to Cart" button.
   ```javascript
   const addToCart = async (productId) => {
     const res = await fetch('http://localhost:5000/cart', {
       method: 'POST',
       headers: { 'Content-Type': 'application/json' },
       body: JSON.stringify({ productId, quantity: 1 }),
     });
     const data = await res.json();
     alert(data.message);
   };

   // Inside the product card:
   <button
     onClick={() => addToCart(product.id)}
     className="bg-blue-500 text-white p-2 rounded mt-2"
   >
     Add to Cart
   </button>
   ```

2. **Display Cart Count**:
   - Add a cart count indicator in the navbar.
   - Create a `CartContext` to manage cart state globally.
   ```javascript
   // Create a new file: context/CartContext.js
   import { createContext, useState, useEffect } from 'react';

   export const CartContext = createContext();

   export const CartProvider = ({ children }) => {
     const [cart, setCart] = useState({});

     const fetchCart = async () => {
       const res = await fetch('http://localhost:5000/cart');
       const data = await res.json();
       setCart(data.cart);
     };

     useEffect(() => {
       fetchCart();
     }, []);

     return (
       <CartContext.Provider value={{ cart, fetchCart }}>
         {children}
       </CartContext.Provider>
     );
   };
   ```

   - Wrap your app with the `CartProvider` in `_app.js`:
   ```javascript
   import { CartProvider } from '../context/CartContext';
   import '../styles/globals.css';

   function MyApp({ Component, pageProps }) {
     return (
       <CartProvider>
         <Component {...pageProps} />
       </CartProvider>
     );
   }

   export default MyApp;
   ```

   - Display the cart count in the navbar:
   ```javascript
   import { useContext } from 'react';
   import { CartContext } from '../context/CartContext';

   export default function Navbar() {
     const { cart } = useContext(CartContext);
     const cartCount = Object.values(cart).reduce((sum, qty) => sum + qty, 0);

     return (
       <nav className="bg-blue-600 p-4 text-white">
         <div className="container mx-auto flex justify-between">
           <h1 className="text-xl font-bold">Mini E-Commerce</h1>
           <div>
             Cart ({cartCount})
           </div>
         </div>
       </nav>
     );
   }
   ```

---

### **Step 2: Implement Cart Page**
1. **Display Cart Items**:
   - Update `pages/cart.js` to fetch and display cart items with product details.
   ```javascript
   import { useContext, useEffect, useState } from 'react';
   import { CartContext } from '../context/CartContext';

   export default function Cart() {
     const { cart } = useContext(CartContext);
     const [products, setProducts] = useState([]);

     useEffect(() => {
       const fetchProducts = async () => {
         const productIds = Object.keys(cart);
         const res = await fetch(`http://localhost:5000/products?ids=${productIds.join(',')}`);
         const data = await res.json();
         setProducts(data);
       };

       fetchProducts();
     }, [cart]);

     return (
       <div className="container mx-auto p-4">
         <h1 className="text-2xl font-bold mb-4">Cart</h1>
         <div className="space-y-4">
           {products.map((product) => (
             <div key={product.id} className="border p-4 rounded">
               <h2 className="text-xl font-semibold">{product.name}</h2>
               <p className="text-gray-600">{product.description}</p>
               <p className="text-green-600 font-bold">${product.price}</p>
               <p className="text-yellow-500">Quantity: {cart[product.id]}</p>
             </div>
           ))}
         </div>
       </div>
     );
   }
   ```

2. **Add "Remove from Cart" Button**:
   - Add a button to remove items from the cart.
   ```javascript
   const removeFromCart = async (productId) => {
     const res = await fetch(`http://localhost:5000/cart/${productId}`, {
       method: 'DELETE',
     });
     const data = await res.json();
     alert(data.message);
     fetchCart(); // Refresh cart
   };

   // Inside the cart item:
   <button
     onClick={() => removeFromCart(product.id)}
     className="bg-red-500 text-white p-2 rounded mt-2"
   >
     Remove
   </button>
   ```

---

### **Step 3: Implement Checkout Page**
1. Create a `pages/checkout.js` file:
   ```javascript
   import { useContext } from 'react';
   import { CartContext } from '../context/CartContext';

   export default function Checkout() {
     const { cart } = useContext(CartContext);

     const handleCheckout = async () => {
       const res = await fetch('http://localhost:5000/checkout', {
         method: 'POST',
         headers: { 'Content-Type': 'application/json' },
         body: JSON.stringify({ customerId: 1, items: Object.keys(cart).map((id) => ({
           productId: id,
           quantity: cart[id],
           price: 10, // Replace with actual price from products
         })) }),
       });
       const data = await res.json();
       alert(data.message);
     };

     return (
       <div className="container mx-auto p-4">
         <h1 className="text-2xl font-bold mb-4">Checkout</h1>
         <button
           onClick={handleCheckout}
           className="bg-green-500 text-white p-2 rounded"
         >
           Place Order
         </button>
       </div>
     );
   }
   ```

---

### **Step 4: Wrap Up**
1. Commit your code to GitHub:
   ```bash
   git add .
   git commit -m "Day 4: Implemented frontend functionality for browsing, filtering, and cart management"
   git push origin main
   ```

---

Absolutely! Let’s break **Day 5** into a **step-by-step process** so you can implement the checkout flow and order processing. I’ll keep it simple and actionable.

---

# **Day 5: Checkout & Order Processing**
**Goal**: Implement the checkout flow, validate orders, and store them in the database.

---

### **Step 1: Enhance the Checkout Page**
1. **Collect Customer Information**:
   - Update `pages/checkout.js` to collect customer details (name, email, address) during checkout.
   ```javascript
   import { useContext, useState } from 'react';
   import { CartContext } from '../context/CartContext';

   export default function Checkout() {
     const { cart } = useContext(CartContext);
     const [customer, setCustomer] = useState({
       name: '',
       email: '',
       address: '',
     });

     const handleInputChange = (e) => {
       const { name, value } = e.target;
       setCustomer({ ...customer, [name]: value });
     };

     const handleCheckout = async () => {
       if (!customer.name || !customer.email || !customer.address) {
         alert('Please fill in all customer details');
         return;
       }

       const res = await fetch('http://localhost:5000/checkout', {
         method: 'POST',
         headers: { 'Content-Type': 'application/json' },
         body: JSON.stringify({
           customer,
           items: Object.keys(cart).map((id) => ({
             productId: id,
             quantity: cart[id],
             price: 10, // Replace with actual price from products
           })),
         }),
       });
       const data = await res.json();
       alert(data.message);
     };

     return (
       <div className="container mx-auto p-4">
         <h1 className="text-2xl font-bold mb-4">Checkout</h1>
         <div className="space-y-4">
           <input
             type="text"
             name="name"
             placeholder="Name"
             value={customer.name}
             onChange={handleInputChange}
             className="w-full p-2 border rounded"
           />
           <input
             type="email"
             name="email"
             placeholder="Email"
             value={customer.email}
             onChange={handleInputChange}
             className="w-full p-2 border rounded"
           />
           <textarea
             name="address"
             placeholder="Address"
             value={customer.address}
             onChange={handleInputChange}
             className="w-full p-2 border rounded"
           />
           <button
             onClick={handleCheckout}
             className="bg-green-500 text-white p-2 rounded"
           >
             Place Order
           </button>
         </div>
       </div>
     );
   }
   ```

---

### **Step 2: Update Backend for Checkout**
1. **Add Customer to Database**:
   - Update the `POST /checkout` endpoint in `index.js` to save customer details.
   ```javascript
   app.post('/checkout', async (req, res) => {
     const { customer, items } = req.body;

     // Save customer details
     const customerQuery = 'INSERT INTO Customers (name, email, address) VALUES (?, ?, ?)';
     db.query(customerQuery, [customer.name, customer.email, customer.address], (err, results) => {
       if (err) throw err;
       const customerId = results.insertId;

       // Create order
       const orderQuery = 'INSERT INTO Orders (customer_id, total_amount, status) VALUES (?, ?, ?)';
       const totalAmount = items.reduce((sum, item) => sum + item.price * item.quantity, 0);
       db.query(orderQuery, [customerId, totalAmount, 'pending'], (err, results) => {
         if (err) throw err;
         const orderId = results.insertId;

         // Add order items
         for (const item of items) {
           const orderItemQuery = 'INSERT INTO OrderItems (order_id, product_id, quantity, price) VALUES (?, ?, ?, ?)';
           db.query(orderItemQuery, [orderId, item.productId, item.quantity, item.price], (err) => {
             if (err) throw err;
           });

           // Update product stock
           const updateStockQuery = 'UPDATE Products SET stock = stock - ? WHERE id = ?';
           db.query(updateStockQuery, [item.quantity, item.productId], (err) => {
             if (err) throw err;
           });
         }

         res.json({ message: 'Order placed successfully', orderId });
       });
     });
   });
   ```

---

### **Step 3: Display Order Confirmation**
1. **Create an Order Confirmation Page** (`pages/order/[id].js`):
   - Display order details after checkout.
   ```javascript
   import { useRouter } from 'next/router';
   import { useEffect, useState } from 'react';

   export default function OrderConfirmation() {
     const router = useRouter();
     const { id } = router.query;
     const [order, setOrder] = useState(null);

     useEffect(() => {
       if (id) {
         fetchOrder();
       }
     }, [id]);

     const fetchOrder = async () => {
       const res = await fetch(`http://localhost:5000/orders/${id}`);
       const data = await res.json();
       setOrder(data);
     };

     if (!order) return <p>Loading...</p>;

     return (
       <div className="container mx-auto p-4">
         <h1 className="text-2xl font-bold mb-4">Order Confirmation</h1>
         <p>Order ID: {order.id}</p>
         <p>Total Amount: ${order.total_amount}</p>
         <p>Status: {order.status}</p>
       </div>
     );
   }
   ```

2. **Redirect to Order Confirmation Page**:
   - Update the `handleCheckout` function in `pages/checkout.js` to redirect after placing an order.
   ```javascript
   const handleCheckout = async () => {
     const res = await fetch('http://localhost:5000/checkout', {
       method: 'POST',
       headers: { 'Content-Type': 'application/json' },
       body: JSON.stringify({
         customer,
         items: Object.keys(cart).map((id) => ({
           productId: id,
           quantity: cart[id],
           price: 10, // Replace with actual price from products
         })),
       }),
     });
     const data = await res.json();
     router.push(`/order/${data.orderId}`);
   };
   ```

---

### **Step 4: Wrap Up**
1. Commit your code to GitHub:
   ```bash
   git add .
   git commit -m "Day 5: Implemented checkout flow and order processing"
   git push origin main
   ```

---



