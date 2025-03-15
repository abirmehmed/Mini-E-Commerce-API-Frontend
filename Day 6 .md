

# **Day 6: Final Enhancements & Deployment**
**Goal**: Add loading states, error handling, pagination, and deploy the project.

---

### **Step 1: Add Loading States**
1. **Home Page** (`pages/index.js`):
   - Add a loading state while fetching products and categories.
   ```javascript
   const [loading, setLoading] = useState(true);

   useEffect(() => {
     const fetchData = async () => {
       await fetchProducts();
       await fetchCategories();
       setLoading(false);
     };
     fetchData();
   }, []);

   if (loading) return <p className="text-center">Loading...</p>;
   ```

2. **Product Details Page** (`pages/products/[id].js`):
   - Add a loading state while fetching product details.
   ```javascript
   if (!product) return <p className="text-center">Loading...</p>;
   ```

3. **Cart Page** (`pages/cart.js`):
   - Add a loading state while fetching cart items.
   ```javascript
   const [loading, setLoading] = useState(true);

   useEffect(() => {
     fetchProducts().then(() => setLoading(false));
   }, [cart]);

   if (loading) return <p className="text-center">Loading...</p>;
   ```

---

### **Step 2: Add Error Handling**
1. **Home Page** (`pages/index.js`):
   - Add error handling for fetching products and categories.
   ```javascript
   const [error, setError] = useState(null);

   const fetchProducts = async () => {
     try {
       const query = new URLSearchParams(filters).toString();
       const res = await fetch(`http://localhost:5000/products?${query}`);
       if (!res.ok) throw new Error('Failed to fetch products');
       const data = await res.json();
       setProducts(data);
     } catch (err) {
       setError(err.message);
     }
   };

   if (error) return <p className="text-center text-red-500">{error}</p>;
   ```

2. **Product Details Page** (`pages/products/[id].js`):
   - Add error handling for fetching product details.
   ```javascript
   const [error, setError] = useState(null);

   const fetchProduct = async () => {
     try {
       const res = await fetch(`http://localhost:5000/products/${id}`);
       if (!res.ok) throw new Error('Failed to fetch product');
       const data = await res.json();
       setProduct(data);
     } catch (err) {
       setError(err.message);
     }
   };

   if (error) return <p className="text-center text-red-500">{error}</p>;
   ```

3. **Cart Page** (`pages/cart.js`):
   - Add error handling for fetching cart items.
   ```javascript
   const [error, setError] = useState(null);

   const fetchProducts = async () => {
     try {
       const productIds = Object.keys(cart);
       const res = await fetch(`http://localhost:5000/products?ids=${productIds.join(',')}`);
       if (!res.ok) throw new Error('Failed to fetch cart items');
       const data = await res.json();
       setProducts(data);
     } catch (err) {
       setError(err.message);
     }
   };

   if (error) return <p className="text-center text-red-500">{error}</p>;
   ```

---

### **Step 3: Implement Pagination**
1. **Backend**:
   - Update the `GET /products` endpoint to support pagination.
   ```javascript
   app.get('/products', (req, res) => {
     const { page = 1, limit = 10 } = req.query;
     const offset = (page - 1) * limit;

     let query = 'SELECT * FROM Products LIMIT ? OFFSET ?';
     const params = [parseInt(limit), parseInt(offset)];

     db.query(query, params, (err, results) => {
       if (err) throw err;
       res.json(results);
     });
   });
   ```

2. **Frontend**:
   - Add pagination controls to the home page (`pages/index.js`).
   ```javascript
   const [page, setPage] = useState(1);
   const [limit, setLimit] = useState(10);

   const fetchProducts = async () => {
     const query = new URLSearchParams({ ...filters, page, limit }).toString();
     const res = await fetch(`http://localhost:5000/products?${query}`);
     const data = await res.json();
     setProducts(data);
   };

   useEffect(() => {
     fetchProducts();
   }, [page, limit]);

   // Pagination controls
   <div className="flex justify-center mt-4">
     <button
       onClick={() => setPage((prev) => Math.max(prev - 1, 1))}
       disabled={page === 1}
       className="bg-blue-500 text-white p-2 rounded mx-2"
     >
       Previous
     </button>
     <button
       onClick={() => setPage((prev) => prev + 1)}
       className="bg-blue-500 text-white p-2 rounded mx-2"
     >
       Next
     </button>
   </div>
   ```

---

### **Step 4: Deploy the Project**
1. **Backend**:
   - Deploy the backend on **Render**:
     1. Push your backend code to GitHub.
     2. Go to [Render](https://render.com), create a new web service, and connect your GitHub repository.
     3. Set the environment variables (e.g., `DB_HOST`, `DB_USER`, `DB_PASSWORD`, `JWT_SECRET`).
     4. Deploy the service.

2. **Frontend**:
   - Deploy the frontend on **Vercel**:
     1. Push your frontend code to GitHub.
     2. Go to [Vercel](https://vercel.com), create a new project, and connect your GitHub repository.
     3. Set the environment variables (e.g., `NEXT_PUBLIC_API_URL` for the backend URL).
     4. Deploy the project.

---

### **Step 5: Wrap Up**
1. Commit your code to GitHub:
   ```bash
   git add .
   git commit -m "Day 6: Added final enhancements, error handling, pagination, and deployed the project"
   git push origin main
   ```

---

### **What’s Next?**
- Celebrate! 🎉 You’ve built and deployed a fully functional Mini E-Commerce system in just 6 days. You can now add more features (e.g., customer ratings, admin dashboard) or improve the UI/UX. Great job! 🚀
