

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

