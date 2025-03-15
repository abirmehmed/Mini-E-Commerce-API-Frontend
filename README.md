# Mini-E-Commerce-API-Frontend

Here’s a **6-day guide** to help you build the Mini E-Commerce API + Frontend project. This plan assumes you have some familiarity with Node.js, MySQL, React/Next.js, and Tailwind CSS. Each day is structured to ensure steady progress.

---

### **Day 1: Backend Setup & Database Design**
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

By following this guide, you should be able to complete the project in 6 days. Good luck! 🚀
