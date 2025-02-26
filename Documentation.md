# 📌 Inventory and Finance Tracking System Documentation

## 📖 Problem Statement
Managing inventory and financial transactions is crucial for suppliers, showroom owners, and customers. Traditional systems struggle with inefficiencies in stock tracking, delayed payments, and financial mismanagement. **Inventory and Finance Tracking System** provides a structured, relational database to efficiently manage transactions, ensuring smooth and transparent business operations.

---

## 📂 Database Tables & Structure

### 1. **Supplier Table**
Stores details of suppliers providing inventory to showrooms.
```sql
CREATE TABLE supplier (
    supplier_id VARCHAR(15) PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    phone VARCHAR(15) UNIQUE NOT NULL,
    email VARCHAR(100) UNIQUE NOT NULL,
    address VARCHAR(255) NOT NULL
);
```

### 2. **Showroom Table**
Stores showroom details and owner information.
```sql
CREATE TABLE showroom (
    showroom_owner_id VARCHAR(15) PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    phone VARCHAR(15) UNIQUE NOT NULL,
    email VARCHAR(100) UNIQUE NOT NULL,
    address VARCHAR(255) NOT NULL
);
```

### 3. **Customer Table**
Stores details of customers purchasing from showrooms.
```sql
CREATE TABLE customer (
    customer_id VARCHAR(15) PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    phone VARCHAR(15) UNIQUE NOT NULL,
    email VARCHAR(100) UNIQUE NOT NULL,
    address VARCHAR(255) NOT NULL
);
```

### 4. **Inventory Table**
Stores details of inventory managed by showrooms.
```sql
CREATE TABLE inventoryasshowroom (
    inventory_id VARCHAR(40) PRIMARY KEY,
    showroom_owner_id VARCHAR(15) NOT NULL,
    product_name VARCHAR(255) NOT NULL,
    quantity INT NOT NULL CHECK (quantity >= 0),
    price DECIMAL(10,2) NOT NULL,
    FOREIGN KEY (showroom_owner_id) REFERENCES showroom(showroom_owner_id)
);
```

### 5. **Supplier Purchases Table**
Tracks inventory purchases from suppliers.
```sql
CREATE TABLE purchases_from_supplier (
    purchase_id VARCHAR(40) PRIMARY KEY,
    supplier_id VARCHAR(15) NOT NULL,
    inventory_id VARCHAR(40) NOT NULL,
    purchase_date DATE NOT NULL,
    quantity INT NOT NULL CHECK (quantity > 0),
    total_cost DECIMAL(10,2) NOT NULL CHECK (total_cost >= 0),
    FOREIGN KEY (supplier_id) REFERENCES supplier(supplier_id),
    FOREIGN KEY (inventory_id) REFERENCES inventoryasshowroom(inventory_id)
);
```

### 6. **Customer Purchases Table**
Tracks sales made by showrooms to customers.
```sql
CREATE TABLE purchases_by_customer_from_showroom (
    purchases_date DATE NOT NULL,
    inventory_item VARCHAR(255) NOT NULL,
    showroom_id VARCHAR(15) NOT NULL,
    customer_id VARCHAR(15) NOT NULL,
    quantity_supplied INT NOT NULL CHECK (quantity_supplied > 0),
    totalAmount DECIMAL(10,2) NOT NULL CHECK (totalAmount >= 0),
    amount_paid DECIMAL(10,2) NOT NULL CHECK (amount_paid >= 0),
    amount_pending DECIMAL(10,2) NOT NULL CHECK (amount_pending >= 0),
    FOREIGN KEY (showroom_id) REFERENCES showroom(showroom_owner_id),
    FOREIGN KEY (customer_id) REFERENCES customer(customer_id)
);
```

---

## 📊 Sample Data with Related Records

```sql
-- Suppliers
INSERT INTO supplier VALUES ('9876543210', 'ABC Supplies', '9876543210', 'abc@supplies.com', 'New York');
INSERT INTO supplier VALUES ('8765432109', 'XYZ Traders', '8765432109', 'xyz@traders.com', 'Los Angeles');

-- Showrooms
INSERT INTO showroom VALUES ('7654321098', 'Mega Showroom', '7654321098', 'mega@showroom.com', 'Chicago');
INSERT INTO showroom VALUES ('6543210987', 'Smart Electronics', '6543210987', 'smart@electronics.com', 'Houston');

-- Customers
INSERT INTO customer VALUES ('5432109876', 'John Doe', '5432109876', 'john@example.com', 'Dallas');
INSERT INTO customer VALUES ('4321098765', 'Jane Smith', '4321098765', 'jane@example.com', 'Seattle');

-- Inventory
INSERT INTO inventoryasshowroom VALUES ('INV001', '7654321098', 'Laptop', 50, 700.00);
INSERT INTO inventoryasshowroom VALUES ('INV002', '6543210987', 'Smartphone', 100, 500.00);

-- Purchases from Supplier
INSERT INTO purchases_from_supplier VALUES ('PUR001', '9876543210', 'INV001', '2024-01-10', 30, 21000.00);
INSERT INTO purchases_from_supplier VALUES ('PUR002', '8765432109', 'INV002', '2024-01-15', 50, 25000.00);

-- Purchases by Customer
INSERT INTO purchases_by_customer_from_showroom VALUES ('2024-02-05', 'Laptop', '7654321098', '5432109876', 1, 700.00, 700.00, 0.00);
INSERT INTO purchases_by_customer_from_showroom VALUES ('2024-02-10', 'Smartphone', '6543210987', '4321098765', 2, 1000.00, 800.00, 200.00);
```

---

## 📌  MySQL Business Queries

```sql
-- 1. Total quantity of inventory supplied by each supplier
SELECT supplier_id, SUM(quantity) AS total_supplied FROM purchases_from_supplier GROUP BY supplier_id;

-- 2. Total sales amount showroom-wise
SELECT showroom_id, SUM(totalAmount) AS total_sales FROM purchases_by_customer_from_showroom GROUP BY showroom_id;

-- 3. Most sold product of the year
SELECT inventory_item, SUM(quantity_supplied) AS total_sold 
FROM purchases_by_customer_from_showroom
WHERE YEAR(purchases_date) = YEAR(CURDATE()) 
GROUP BY inventory_item 
ORDER BY total_sold DESC 
LIMIT 1;

-- 4. Total due amount showroom-wise
SELECT showroom_id, SUM(amount_pending) AS total_due 
FROM purchases_by_customer_from_showroom 
GROUP BY showroom_id;

-- 5. Customers with highest total purchases
SELECT customer_id, SUM(totalAmount) AS total_spent 
FROM purchases_by_customer_from_showroom 
GROUP BY customer_id 
ORDER BY total_spent DESC 
LIMIT 1;
```

---

## 🚀 **Conclusion**
The **Inventory and Finance Tracking System** ensures a structured, efficient, and scalable approach to **inventory management and financial tracking**. It provides transparency, accurate record-keeping, and better decision-making for suppliers, showroom owners, and customers. 📊✅
