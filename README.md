# Product Inventory System

A modern Product Inventory Management module built with **Laravel 12**, **Inertia.js**, **Vue.js**, and **ShadCN Vue**. This project demonstrates full-stack CRUD operations, file handling, and responsive UI design.

## Tech Stack

* **Framework:** Laravel 12
* **Frontend:** Vue.js 3 (Composition API)
* **Adapter:** Inertia.js
* **UI Library:** ShadCN Vue + Tailwind CSS
* **Database:**  MySQL

---

## Installation & Setup

Follow these steps to get the project running on your local machine.

### 1. Clone the Repository
```bash
git clone https://github.com/saanchita-paul/product_inventory.git
cd product_inventory
````

### 2. Install Backend Dependencies
```bash
composer install
```

### 3. Setup Environment
Copy the example environment file and generate the application key.
```bash
cp .env.example .env
php artisan key:generate
```

### 4. Configure Database
By default, this project uses SQLite. If you prefer MySQL, update DB_CONNECTION in your .env file.

### 5. Run Migrations & Seed Data
This will create the database tables and populate them with 50 dummy products and a test user.
```bash
php artisan migrate:fresh --seed
```

### Test User Login:
```bash
Email: test@example.com
Password: password
```

### 6. Link Storage
Create the symbolic link to allow public access to uploaded images.
```bash
php artisan storage:link
```

### 7. Install Frontend Dependencies & Build
```bash
npm install
npm run build
```

### 8. Start the Server
You can run the development server (keep this terminal open):
```bash
npm run dev
```
In a separate terminal, serve the Laravel application:
```bash
php artisan serve
```

**Visit http://localhost:8000 in your browser.**

## Technical Assessment Questions
1. Laravel 12 & Modern PHP: Dependency Injection vs. Facade


   **Q: What is the difference between Dependency Injection and using a Facade in Laravel? In which scenario would you prefer using Dependency Injection?**
   - Difference:
        - Dependency Injection (DI) involves passing dependencies (objects/classes) explicitly into a class, usually via the constructor. This makes dependencies visible and the class loosely coupled.
        - Facades provide a "static" interface to classes available in the application's service container. They offer syntactic sugar (e.g., Route::get()) but hide the actual underlying dependency implementation.

   - Preference:
        - I prefer Dependency Injection when writing complex business logic, Services, or Controllers that require Unit Testing. DI allows me to easily "mock" or swap out dependencies during tests (e.g., mocking a PaymentService so I don't charge a real credit card during a test). Facades can make testing harder because they rely on global state.
     
2. Performance: Optimizing for 10,000 Products


   **Q: In a real-world scenario with 10,000 products, how would you optimize the search query to ensure the page loads quickly?**

   1. Database Indexing: The most critical step is to add indexes to the searchable columns in the migration ($table->index(['name', 'sku']);). This changes the database search from a slow full-table scan to a fast lookup.

   2. Select Specific Columns: Instead of Product::all(), I would use select('id', 'name', 'sku', 'price') to fetch only necessary data, reducing memory usage.

   3. Full-Text Search (Laravel Scout): For fuzzy matching (e.g., finding "phone" inside "smartphone") on large datasets, SQL LIKE queries are slow. I would implement Laravel Scout with a driver like Meilisearch or Algolia for optimized, millisecond-speed search results.

   4. Debouncing: On the frontend, I used lodash. debounce to wait 300ms after the user stops typing before sending the search request, preventing the server from being flooded with queries.

3. Security: CSRF & Inertia.js


   **Q: How does Laravel's CSRF protection work with Inertia.js, and why is it important for form submissions?**
 
   - How it works: Laravel automatically includes a XSRF-TOKEN cookie in every response. When Inertia (via Axios) makes a request, it reads this cookie and sends it back in the X-XSRF-TOKEN header. Laravel's middleware verifies that the token in the header matches the one in the session.

   - Importance: It prevents Cross-Site Request Forgery attacks. Without it, a malicious website could force a logged-in user's browser to send a silent POST request to my site (e.g., /products/delete/1) without the user's consent. The token ensures the request actually originated from my application.

4. Code Structure: The N+1 Query Problem


   **Q: Explain the N+1 query problem. If each Product belonged to a Category, how would you prevent N+1 issues when loading the list?**

 - Explanation: The N+1 problem happens when code executes 1 query to fetch a list of items (e.g., 10 products), and then executes an additional query inside a loop for every single item to fetch related data (e.g., 10 queries for categories). This results in 11 queries total instead of 2.

 - Prevention: I would use Eager Loading using the with() method.

      - Bad: Product::all() (then accessing $product->category->name in the loop).

      - Good: Product::with('category')->get().

      - This forces Laravel to fetch all products in Query 1, collect all category IDs, and fetch all matching categories in Query 2, mapping them automatically.


## Features Implemented

- CRUD Operations: Create, Read, Update, Delete products.

- Image Uploads: Handled via a dedicated Service class with server-side storage.

- Search & Pagination: Real-time search by Name/SKU and paginated results.

- Validation: Strict backend validation (Unique SKU, Max file size, etc.) + Client-side file size checks.

- Soft Deletes: Products are archived instead of permanently deleted.

- Modern UI: Built with ShadCN Vue components (Dialogs, Tables, Dropdowns).
