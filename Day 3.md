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

