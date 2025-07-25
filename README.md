
# 📚 **Library Management System**

## 🔍 **Overview**

**Title**: Library Management System
**Skill Level**: Intermediate
**Database Name**: `library_db`

---

## 🎯 **Goals**

1. **Build the Library Database**: Create and populate tables for branches, staff, members, books, issued books, and returned books.
2. **CRUD Operations**: Add, view, modify, and remove records efficiently.
3. **Use CTAS (Create Table As Select)**: Generate new tables from existing query results.
4. **Advanced SQL Queries**: Write complex queries for meaningful insights.

---

## 🗂 **System Structure**

* **Database Setup**: Database named `library_db`.
* **Tables Created**:

  * `branch` 🏢
  * `employees` 👩‍💼
  * `members` 👤
  * `books` 📕
  * `issued_status` 📤
  * `return_status` 📥

---

### ✅ **Database & Tables**

```sql
CREATE DATABASE library_db;

-- Branch Table
CREATE TABLE branch (
    branch_id VARCHAR(10) PRIMARY KEY,
    manager_id VARCHAR(10),
    branch_address VARCHAR(30),
    contact_no VARCHAR(15)
);

-- Employees Table
CREATE TABLE employees (
    emp_id VARCHAR(10) PRIMARY KEY,
    emp_name VARCHAR(30),
    position VARCHAR(30),
    salary DECIMAL(10,2),
    branch_id VARCHAR(10),
    FOREIGN KEY (branch_id) REFERENCES branch(branch_id)
);

-- Members Table
CREATE TABLE members (
    member_id VARCHAR(10) PRIMARY KEY,
    member_name VARCHAR(30),
    member_address VARCHAR(30),
    reg_date DATE
);

-- Books Table
CREATE TABLE books (
    isbn VARCHAR(50) PRIMARY KEY,
    book_title VARCHAR(80),
    category VARCHAR(30),
    rental_price DECIMAL(10,2),
    status VARCHAR(10),
    author VARCHAR(30),
    publisher VARCHAR(30)
);

-- Issued Books Table
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

-- Returned Books Table
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

## 🛠 **CRUD Operations**

* **Insert Example (Add a Book)**:

```sql
INSERT INTO books(isbn, book_title, category, rental_price, status, author, publisher)
VALUES('978-1-60129-456-2', 'To Kill a Mockingbird', 'Classic', 6.00, 'yes', 'Harper Lee', 'J.B. Lippincott & Co.');
```

* **Update Example (Change Member Address)**:

```sql
UPDATE members
SET member_address = '125 Oak St'
WHERE member_id = 'C103';
```

* **Delete Example (Remove Issued Record)**:

```sql
DELETE FROM issued_status WHERE issued_id = 'IS121';
```

* **Retrieve Example (Books Issued by Employee E101)**:

```sql
SELECT * FROM issued_status WHERE issued_emp_id = 'E101';
```

---

## 🧠 **Key Tasks & Queries**

### 🔍 Query Highlights:

1. **Members Issuing More Than One Book**

```sql
SELECT issued_emp_id, COUNT(*) 
FROM issued_status 
GROUP BY 1 
HAVING COUNT(*) > 1;
```

2. **Create Summary Table (Books & Issue Count)**

```sql
CREATE TABLE book_issued_cnt AS
SELECT b.isbn, b.book_title, COUNT(ist.issued_id) AS issue_count
FROM issued_status ist
JOIN books b ON ist.issued_book_isbn = b.isbn
GROUP BY b.isbn, b.book_title;
```

3. **Top Categories Revenue**

```sql
SELECT b.category, SUM(b.rental_price), COUNT(*)
FROM issued_status ist
JOIN books b ON b.isbn = ist.issued_book_isbn
GROUP BY b.category;
```

4. **Overdue Books (30 Days)**

```sql
SELECT ist.issued_member_id, m.member_name, bk.book_title,
CURRENT_DATE - ist.issued_date AS overdue_days
FROM issued_status ist
JOIN members m ON m.member_id = ist.issued_member_id
JOIN books bk ON bk.isbn = ist.issued_book_isbn
WHERE CURRENT_DATE - ist.issued_date > 30;
```

---

## 📑 **Advanced Features**

* **Stored Procedures**:

  * `add_return_records` – Automatically updates return details and book status.
  * `issue_book` – Issues book if available, otherwise displays an error.
* **Reports**:

  * Branch performance, active members, top-performing employees.

---

## 🏆 **Outcome**

This project showcases **database design**, **data handling**, and **complex SQL queries** to manage library operations effectively. It demonstrates **real-world database management skills** through **reporting**, **automation**, and **analytics**
