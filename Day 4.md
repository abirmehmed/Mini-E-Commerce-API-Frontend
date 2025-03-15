

### **Day 4: Frontend Functionality**
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
