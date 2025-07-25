
# 📚 **Library Management System**

## 🔍 **Project Snapshot**

**Title**: Library Management System
**Complexity**: Intermediate Level
**Database Name**: `library_db`

---

## 🎯 **Goals of the Project**

1. **Build the Library Database**: Design and implement a database containing branches, employees, members, books, and transaction records for book issuance and returns.
2. **Perform CRUD Operations**: Insert, update, delete, and retrieve records from the tables.
3. **CTAS (Create Table As Select)**: Generate new tables from existing datasets using queries.
4. **Write Advanced SQL Queries**: Execute analytical and complex queries for insights and reports.

---

## 🏗 **System Blueprint**

* **Database Setup**: Create a database named `library_db`.
* **Tables Included**:

  * Branch Information
  * Employee Details
  * Member Records
  * Books Inventory
  * Issue & Return Tracking

### ✅ **Database Schema**

```sql
CREATE DATABASE library_db;

-- Branch Details
DROP TABLE IF EXISTS branch;
CREATE TABLE branch (
    branch_id VARCHAR(10) PRIMARY KEY,
    manager_id VARCHAR(10),
    branch_address VARCHAR(30),
    contact_no VARCHAR(15)
);

-- Employee Table
DROP TABLE IF EXISTS employees;
CREATE TABLE employees (
    emp_id VARCHAR(10) PRIMARY KEY,
    emp_name VARCHAR(30),
    position VARCHAR(30),
    salary DECIMAL(10,2),
    branch_id VARCHAR(10),
    FOREIGN KEY (branch_id) REFERENCES branch(branch_id)
);

-- Member Table
DROP TABLE IF EXISTS members;
CREATE TABLE members (
    member_id VARCHAR(10) PRIMARY KEY,
    member_name VARCHAR(30),
    member_address VARCHAR(30),
    reg_date DATE
);

-- Books Table
DROP TABLE IF EXISTS books;
CREATE TABLE books (
    isbn VARCHAR(50) PRIMARY KEY,
    book_title VARCHAR(80),
    category VARCHAR(30),
    rental_price DECIMAL(10,2),
    status VARCHAR(10),
    author VARCHAR(30),
    publisher VARCHAR(30)
);

-- Issued Status
DROP TABLE IF EXISTS issued_status;
CREATE TABLE issued_status (
    issued_id VARCHAR(10) PRIMARY KEY,
    issued_member_id VARCHAR(30),
    issued_book_name VARCHAR(80),
    issued_date DATE,
    issued_book_isbn VARCHAR(50),
    issued_emp_id VARCHAR(10),
    FOREIGN KEY (issued_member_id) REFERENCES members(member_id),
    FOREIGN KEY (issued_emp_id) REFERENCES employees(emp_id),
    FOREIGN KEY (issued_book_isbn) REFERENCES books(isbn)
);

-- Return Status
DROP TABLE IF EXISTS return_status;
CREATE TABLE return_status (
    return_id VARCHAR(10) PRIMARY KEY,
    issued_id VARCHAR(30),
    return_book_name VARCHAR(80),
    return_date DATE,
    return_book_isbn VARCHAR(50),
    FOREIGN KEY (return_book_isbn) REFERENCES books(isbn)
);
```

---

## 🛠 **Core Functionalities (CRUD)**

### **📌 Add a New Book**

```sql
INSERT INTO books(isbn, book_title, category, rental_price, status, author, publisher)
VALUES ('978-1-60129-456-2', 'To Kill a Mockingbird', 'Classic', 6.00, 'yes', 'Harper Lee', 'J.B. Lippincott & Co.');
SELECT * FROM books;
```

### **📌 Update Member Address**

```sql
UPDATE members
SET member_address = '125 Oak St'
WHERE member_id = 'C103';
```

### **📌 Delete Issued Record**

```sql
DELETE FROM issued_status WHERE issued_id = 'IS121';
```

### **📌 Find All Books Issued by Employee E101**

```sql
SELECT * FROM issued_status WHERE issued_emp_id = 'E101';
```

### **📌 Members Who Issued More Than 1 Book**

```sql
SELECT issued_emp_id, COUNT(*)
FROM issued_status
GROUP BY 1
HAVING COUNT(*) > 1;
```

---

## 🔄 **CTAS Examples**

**Create a Summary Table for Book Issue Count**

```sql
CREATE TABLE book_issued_cnt AS
SELECT b.isbn, b.book_title, COUNT(ist.issued_id) AS issue_count
FROM issued_status ist
JOIN books b ON ist.issued_book_isbn = b.isbn
GROUP BY b.isbn, b.book_title;
```

---

## 📊 **Analytical Queries**

✔ **Books in ‘Classic’ Category**

```sql
SELECT * FROM books WHERE category = 'Classic';
```

✔ **Total Rental Revenue by Category**

```sql
SELECT b.category, SUM(b.rental_price), COUNT(*)
FROM issued_status ist
JOIN books b ON b.isbn = ist.issued_book_isbn
GROUP BY 1;
```

✔ **Recent Registrations (Last 180 Days)**

```sql
SELECT * FROM members WHERE reg_date >= CURRENT_DATE - INTERVAL '180 days';
```

✔ **Employee & Manager with Branch Details**

```sql
SELECT e1.emp_id, e1.emp_name, e1.position, e1.salary, b.*, e2.emp_name AS manager
FROM employees e1
JOIN branch b ON e1.branch_id = b.branch_id
JOIN employees e2 ON e2.emp_id = b.manager_id;
```

---

## 🔐 **Stored Procedures**

### **Procedure for Book Return and Status Update**

```sql
CREATE OR REPLACE PROCEDURE add_return_records(p_return_id VARCHAR(10), p_issued_id VARCHAR(10), p_book_quality VARCHAR(10))
LANGUAGE plpgsql AS $$
DECLARE v_isbn VARCHAR(50); v_book_name VARCHAR(80);
BEGIN
    INSERT INTO return_status(return_id, issued_id, return_date, book_quality)
    VALUES (p_return_id, p_issued_id, CURRENT_DATE, p_book_quality);
    SELECT issued_book_isbn, issued_book_name INTO v_isbn, v_book_name
    FROM issued_status WHERE issued_id = p_issued_id;
    UPDATE books SET status = 'yes' WHERE isbn = v_isbn;
    RAISE NOTICE 'Book Returned Successfully: %', v_book_name;
END;
$$;
```

---

## 📑 **Reports and Insights**

* **Branch Performance Report** (Issued Books, Returns, Total Revenue)
* **Employee Productivity Analysis**
* **Overdue Book Tracking with Fine Calculation**
* **Top 3 Employees by Issue Count**
* **Active Members in Last 2 Months**

---

### ✅ **Outcome**

This project demonstrates **real-world SQL applications**, including:

* **Database Design**
* **Transaction Handling**
* **Data Aggregation**
* **Advanced Query Writing**
* **Stored Procedures for Automation**


