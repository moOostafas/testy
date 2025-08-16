# Ecommerce
PHP & MySQL-based E-Commerce system that allows customers to browse products, manage their shopping cart, and place orders, while providing administrators with a dashboard to manage the store.

## Database Scheme
The database has 5 tables:

### Users Table
> [!NOTE]
> role of the user enables admin access dashboard.

```SQL
-- users
CREATE TABLE users (
  id INT AUTO_INCREMENT PRIMARY KEY,
  name VARCHAR(255) NOT NULL,
  email VARCHAR(255) NOT NULL UNIQUE,
  password VARCHAR(255) NOT NULL,
  role ENUM('customer', 'admin') DEFAULT 'customer',
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

### Categories Table
> [!NOTE]
> category name is unique

```SQL
-- categories
CREATE TABLE categories (
  id INT AUTO_INCREMENT PRIMARY KEY,
  name VARCHAR(255) NOT NULL UNIQUE,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

### Products Table
> [!NOTE]
> product can be categorized or uncategorized, has image & description or one or neither.

```SQL
-- products
CREATE TABLE products (
  id INT AUTO_INCREMENT PRIMARY KEY,
  name VARCHAR(255) NOT NULL,
  description TEXT,
  price DECIMAL(10, 2) NOT NULL,
  image VARCHAR(255),
  category_id INT,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  FOREIGN KEY (category_id) REFERENCES categories(id) ON DELETE SET NULL
);
```

### Orders Table
> [!NOTE]
> orders has calculated total price so that we can keep tracing old orders while prices changing.

```SQL
-- orders
CREATE TABLE orders (
  id INT AUTO_INCREMENT PRIMARY KEY,
  user_id INT NOT NULL,
  total_price DECIMAL(10, 2) NOT NULL,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE CASCADE
);
```

### Order Items Table
> [!NOTE]
> each order could contain many items so that it's a junction table for orders and products placed in that order.

```SQL
-- order_items
CREATE TABLE order_items (
  order_id INT NOT NULL,
  product_id INT NOT NULL,
  quantity INT NOT NULL,
  PRIMARY KEY (order_id, product_id),
  FOREIGN KEY (order_id) REFERENCES orders(id) ON DELETE CASCADE,
  FOREIGN KEY (product_id) REFERENCES products(id) ON DELETE RESTRICT
);
```

## Instructions
1. [download xampp](https://www.apachefriends.org/download.html)

2. start xampp Apache & MySQL

3. Move ecommerce folder directly inside htdocs folder (When you open ecommerce on htdocs the path appears as `C:\xampp\htdocs\ecommerce`)

4. Create Database `store` in phpmyadmin (`utf8mb4_general_ci`)

5. In SQL tab code this scheme
```SQL
-- users
CREATE TABLE users (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(255) NOT NULL,
    email VARCHAR(255) NOT NULL UNIQUE,
    password VARCHAR(255) NOT NULL,
    role ENUM('customer', 'admin') DEFAULT 'customer',
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- categories
CREATE TABLE categories (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(255) NOT NULL UNIQUE,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- products
CREATE TABLE products (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(255) NOT NULL,
    description TEXT,
    price DECIMAL(10, 2) NOT NULL,
    image VARCHAR(255),
    category_id INT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (category_id) REFERENCES categories(id) ON DELETE SET NULL
);

-- orders
CREATE TABLE orders (
    id INT AUTO_INCREMENT PRIMARY KEY,
    user_id INT NOT NULL,
    total_price DECIMAL(10, 2) NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE CASCADE
);

-- order_items
CREATE TABLE order_items (
    order_id INT NOT NULL,
    product_id INT NOT NULL,
    quantity INT NOT NULL,
    PRIMARY KEY (order_id, product_id),
    FOREIGN KEY (order_id) REFERENCES orders(id) ON DELETE CASCADE,
    FOREIGN KEY (product_id) REFERENCES products(id) ON DELETE RESTRICT
);
```

6. Open [localhost](http://localhost/ecommerce/) on ecommerce folder to navigate in website as a guest (non-auth user).

7. you can create a user through register to confirm orders in cart page and logout.

8. you must inject an admin into database manually to get into dashboard through login.
```SQL
INSERT INTO users (name, email, password, role) VALUES('Dania Admin I', 'admin1@example.com', '$2y$10$G9HM8jMaCCuGDYJ0CgEgHeXxMc3qjfhWaq3IGg6oUeU81IQmQjR/W', 'admin');
```
> [!NOTE]
> The password is hashed so that it will be identical to one hashed from your login.

9. login as admin with `admin1@example.com` (email) & `admin123` (pass).
