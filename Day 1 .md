
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

