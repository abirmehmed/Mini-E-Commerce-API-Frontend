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

