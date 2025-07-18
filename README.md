# Ecommerce Management System (DBMS)

A web-based e-commerce platform built using Flask and MySQL, allowing sellers to manage products and categories, and customers to browse, shop, and manage their cart. The project demonstrates DBMS concepts in a practical application with authentication, role-based access, and product/category management.

## Features

- **User Authentication**: Register as a customer or seller, login/logout functionality.
- **Role-Based Dashboard**:
  - Sellers: Can upload, update, and delete products and categories.
  - Customers: Can browse products, add them to cart, and view/manage cart.
- **Product Management**: Sellers can add new products, update details, upload images, categorize products, and assign categories.
- **Category Management**: Create new categories, delete categories (sellers only).
- **Shopping Cart**: Customers can add products to their cart, view total price, and see cart contents.
- **Responsive UI**: Built with Bootstrap for modern look and feel.
- **Image Uploads**: Product images are stored and rendered dynamically.

## Technologies Used

- **Backend**: Python (Flask)
- **Database**: MySQL (with Flask-MySQLdb for integration)
- **Password Hashing**: bcrypt
- **Frontend**: HTML, Bootstrap, Jinja2 templates
- **SQL**: Used extensively for all core data operations

## SQL Usage Highlights

This project makes extensive use of SQL for interacting with the MySQL database. Some key examples include:

- **User and Seller Management**:  
  - Registering users involves `INSERT INTO Users (...)` statements, with additional entries for sellers in the `Sellers` table.
  - Authentication checks by fetching user data:  
    ```python
    cur.execute("SELECT id, password, role FROM Users WHERE username = %s", (username,))
    ```
- **Product and Category Operations**:  
  - Fetching product and category lists:  
    ```python
    cur.execute("SELECT * FROM category")
    cur.execute("SELECT * FROM Products")
    ```
  - Adding new products (image handled as binary):  
    ```python
    cur.execute("""
        INSERT INTO Products (name, price, description, image, category_id) 
        VALUES (%s, %s, %s, %s, %s)
    """, (name, price, description, image_binary, selected_category))
    ```
  - Updating products and categories, including handling new category creation:
    ```python
    cur.execute("""
        UPDATE Products SET name = %s, price = %s, description = %s, category_id = %s 
        WHERE id = %s
    """, ...)
    ```
  - Deleting categories and all associated products:
    ```python
    cur.execute("DELETE FROM category WHERE category_id = %s", (category_id,))
    ```
- **Shopping Cart**:  
  - Adding items to a user's cart (orders table):  
    ```python
    cur.execute("INSERT INTO Orders (user_id, product_id, quantity) VALUES (%s, %s, %s)", ...)
    ```
  - Querying cart contents and calculating totals (joins and stored procedure):
    ```python
    cur.execute("""
        SELECT Products.id, Products.name, Products.price, Orders.quantity, Orders.id as order_id
        FROM Orders 
        JOIN Products ON Orders.product_id = Products.id 
        WHERE Orders.user_id = %s
    """, (user_id,))
    cur.execute("SELECT CalculateOrderPrice(%s) AS total_price", (item['order_id'],))
    ```

**Stored Procedures/Functions**  
- The code references a stored function `CalculateOrderPrice(order_id)`, demonstrating the use of advanced SQL features for calculating order totals.

**Dynamic Category Creation**
- The app supports adding new categories if they don’t exist, via SQL checks and conditional inserts.

## Setup Instructions

1. **Clone the Repository**
   ```sh
   git clone https://github.com/bcsamrudh/Ecommerce-Management-System-DBMS.git
   cd Ecommerce-Management-System-DBMS
   ```

2. **Install Python Dependencies**
   ```sh
   pip install flask flask-mysqldb bcrypt
   ```

3. **Configure MySQL**
   - Create a MySQL database named `ecommerce_db`.
   - Add required tables:
     - `Users`, `Sellers`, `Products`, `Orders`, `category`, etc.
   - Update credentials in `app.py` if necessary:
     ```python
     app.config['MYSQL_HOST'] = '127.0.0.1'
     app.config['MYSQL_USER'] = 'mysqluser'
     app.config['MYSQL_PASSWORD'] = 'password'
     app.config['MYSQL_DB'] = 'ecommerce_db'
     ```

4. **Run the Application**
   ```sh
   python app.py
   ```
   The server will start on `http://127.0.0.1:5000/`.

## Usage

- **Registration**: Register as a customer or seller; sellers must provide GSTIN.
- **Login**: Access dashboard based on role.
- **Browse Products**: View products by category, see images/descriptions.
- **Add to Cart**: Customers can add products to their shopping cart.
- **Manage Products/Categories**: Sellers can add, update, and delete their products and categories.
- **Upload Images**: Sellers upload images for their products; images are displayed in listings.

## File Structure

- `app.py`: Flask backend, routes, and DB logic.
- `templates/`: HTML templates (`home.html`, `cart.html`, `upload_product.html`, etc.)
- `static/uploads/`: Uploaded product images.
- `requirements.txt`: (Recommended to add dependency list.)

## Author

- [bcsamrudh](https://github.com/bcsamrudh)

---

**Note:** Please ensure you create the necessary MySQL tables and procedures as referenced in the code. For a full DB schema, refer to the source code and add missing DDL as needed.
