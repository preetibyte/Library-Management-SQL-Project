# Library Management Project Using SQL

## Project Overview

**Project Title**: Library Management Project  
**Level**: Intermediate  
**Database**: `library_management_project_db`

## Obejectives

1. **Set up the Library Management System Database**: Create and populate the database with tables for branches, employees, members, books, issued status, and return status.
2. **CRUD Operations**: Perform Create, Read, Update, and Delete operations on the data.
3. **CTAS (Create Table As Select)**: Utilize CTAS to create new tables based on query results.
4. **Advanced SQL Queries**: Develop complex queries to analyze and retrieve specific data.

## Project Structure

### 1. Database Setup

<img width="936" height="593" alt="ER Diagram" src="https://github.com/user-attachments/assets/c03cbdd3-a283-439c-bfb5-90c9c7d498a8" />

- **Database Creation**: Created a database named `library_management_project_db`.
- **Table Creation**: Created tables for branches, employees, members, books, issued status, and return status. Each table includes relevant columns and relationships.

```sql
CREATE DATABASE library_management_project_db;

-- create table "branch"
CREATE TABLE IF NOT EXISTS branch
(
	branch_id varchar(15) PRIMARY KEY,	
	manager_id varchar(15),	
	branch_address varchar(15),	
	contact_no varchar(15)
);

-- create table "employees"
CREATE TABLE IF NOT EXISTS employees
(
	emp_id varchar(10) PRIMARY KEY,
	emp_name varchar(35),
	position varchar(35),
	salary INT,
	branch_id varchar(35)    -- FK
);

-- create table "books"
CREATE TABLE IF NOT EXISTS books
(
	isbn VARCHAR(25) PRIMARY KEY,
	book_title VARCHAR(75),
	category VARCHAR(25),
	rental_price FLOAT,
	status VARCHAR(15),
	author VARCHAR(35),
	publisher VARCHAR(55)
);

-- create table "issued_status"
CREATE TABLE IF NOT EXISTS issued_status
(
	issued_id VARCHAR(10) PRIMARY KEY,
	issued_member_id VARCHAR(10),        -- FK
	issued_book_name VARCHAR(75),
	issued_date DATE,
	issued_book_isbn VARCHAR(25),      -- FK
	issued_emp_id VARCHAR(10)          -- FK
);

-- create table "members"
CREATE TABLE IF NOT EXISTS members
(
	member_id varchar(20) PRIMARY KEY,
	member_name varchar(25),
	member_address varchar(75),
	reg_date date
);

-- create table "return_status"
CREATE TABLE IF NOT EXISTS return_status
(
	return_id VARCHAR(15) PRIMARY KEY,
	issued_id VARCHAR(15),                 -- FK
	return_book_name VARCHAR(75),
	return_date DATE,
	return_book_isbn VARCHAR(20)    -- FK
);

-- ADD Foreign Key
ALTER TABLE employees
ADD CONSTRAINT fk_branch
FOREIGN KEY (branch_id)
REFERENCES branch(branch_id);

ALTER TABLE issued_status
ADD CONSTRAINT fk_members
FOREIGN KEY (issued_member_id)
REFERENCES members (member_id);

ALTER TABLE issued_status
ADD CONSTRAINT fk_isbn
FOREIGN KEY (issued_book_isbn)
REFERENCES books(isbn);

ALTER TABLE issued_status
ADD CONSTRAINT fk_employees
FOREIGN KEY (issued_emp_id )
REFERENCES employees (emp_id);

ALTER TABLE return_status
ADD CONSTRAINT fk_issued_status
FOREIGN KEY (issued_id)
REFERENCES issued_status (issued_id);
```

### 2. CRUD Operations

- **Create**: Inserted sample records into the `books` table.
- **Read**: Retrieved and displayed data from various tables.
- **Update**: Updated records in the `employees` table.
- **Delete**: Removed records from the `members` table as needed.

**Task 1. Create a New Book Record**
-- "978-1-60129-456-2', 'To Kill a Mockingbird', 'Classic', 6.00, 'yes', 'Harper Lee', 'J.B. Lippincott & Co.')"
```sql
INSERT INTO books (isbn, book_tittle, category, rental_price, status, author, publisher)
VALUES ("978-1-60129-456-2", 'To Kill a Mockingbird', 'Classic', 6.00, 'yes', 'Harper Lee', 'J.B. Lippincott & Co.');
```

**Task 2: Update an Existing Member's Address**
```sql
UPDATE members
SET member_address = '125 Main St'
WHERE member_id = 'C101';
select * from members;
```

**Task 3: Delete a Record from the Issued Status Table**
-- Delete the record with issued_id = 'IS121' from the issued_status table.
```sql
SELECT * FROM issued_status
WHERE issued_id = "IS121";

DELETE FROM issued_status
WHERE issued_id = "IS121";
```

**Task 4: Retrieve All Books Issued by a Specific Employee**
--  Select all books issued by the employee with emp_id = 'E101'.
```sql
SELECT * from issued_status
WHERE issued_emp_id = 'E101';
```

**Task 5: List Members Who Have Issued More Than One Book**
-- Use GROUP BY to find members who have issued more than one book.

```sql
SELECT 
	issued_member_id
    -- COUNT(issued_id) as total_book_issued
FROM issued_status
GROUP BY 1
HAVING COUNT(issued_id) > 1;
```

### 3. CTAS (Create Table As Select)

**Task 6: Create Summary Tables**: Used CTAS to generate new tables based on query results - each book and total book_issued_cnt.

```sql
CREATE TABLE book_count
AS 
SELECT 
	b.isbn, b.book_title, COUNT(ist.issued_id) AS issued_count 
FROM books AS b
JOIN issued_status AS ist
	ON ist.issued_book_isbn = b.isbn
GROUP BY 1,2;
```

### 4. Data Analysis & Findings

The following SQL queries were used to address specific questions:

**Task 7. Retrieve All Books in a Specific Category**:

```sql
SELECT 
	book_title, category
FROM books
WHERE category ='Fantasy';
```

**Task 8: Find Total Rental Income by Category**:

```sql
SELECT 
	b.category, SUM(b.rental_price), COUNT(*) AS count_rental_price
FROM books AS b
JOIN issued_status AS ist
ON b.isbn = ist.issued_book_isbn
GROUP BY 1;
```

**Task 9: List Members Who Registered in the Last 180 Days**
-- members table don't have record of registered within 180 days. first insert some record which give appropriate instead of null values.
```sql
INSERT INTO members(member_id, member_name, member_address, reg_date)
VALUES
('C120', 'francis', '156 Bigul St', '2026-05-05'),
('C121', 'Maduro', '196 Begum St', '2025-12-02');

SELECT * FROM members
WHERE reg_date >= DATE_SUB(CURDATE(), INTERVAL 180 DAY);
```

**Task 10. List Employees with Their Branch Manager's Name and their branch details**:
```sql
SELECT 
	e1.*,
    b.manager_id,
    b.branch_address,
    e2.emp_name AS manager_name
FROM employees as e1
JOIN branch AS b 
ON e1.branch_id = b.branch_id
JOIN employees AS e2
ON b.manager_id = e2.emp_id;
```

**Task 11. Create a Table of Books with Rental Price Above a Certain Threshold 7USD**:
```sql
CREATE TABLE expensive_books
AS
SELECT * FROM books
WHERE rental_price > 7;
```

**Task 12: Retrieve the List of Books Not Yet Returned**
```sql
SELECT 
	issued_book_name
FROM issued_status AS ist
LEFT JOIN return_status AS rs
ON ist.issued_id = rs.issued_id
WHERE return_id IS NULL;
```

**Task 13: Identify Members with Overdue Books**  
-- Write a query to identify members who have overdue books (assume a 30-day return period from 30 april 2024). 
Display the member's_id, member's name, book title, issue date, and days overdue.
```sql
-- members == issued_status == return_status
-- filter out the return book
-- overdue > 30
SELECT 
	m.member_id, 
	m.member_name, 
    ist.issued_book_name,
    ist.issued_date, 
    -- rs.return_date,
    DATEDIFF('2024-04-30', ist.issued_date) AS overdue_days
FROM members AS m
JOIN issued_status AS ist
ON m.member_id = ist.issued_member_id
LEFT JOIN return_status AS rs
ON rs.issued_id = ist.issued_id
WHERE rs.return_date IS NULL
AND DATEDIFF('2024-04-30', ist.issued_date) > 30
ORDER BY 1;
```

**Task 14: Branch Performance Report**
-- Create a query that generates a performance report for each branch, showing the number of books issued, 
the number of books returned, and the total revenue generated from book rentals.
```sql
CREATE TABLE banch_report
AS
SELECT 
	br.branch_id,
    br.manager_id,
    COUNT(ist.issued_id) AS Issued_book_number,
    COUNT(rs.return_id) AS Return_book_number,
    SUM(b.rental_price) AS Total_revenue
FROM branch AS br
JOIN employees AS e
ON br.branch_id = e.branch_id
JOIN issued_status AS ist
ON ist.issued_emp_id = e.emp_id
LEFT JOIN return_status AS rs
ON ist.issued_id = rs.issued_id
JOIN books AS b
ON ist.issued_book_isbn = b.isbn 
GROUP BY 1
Order BY 1;
```

**Task 15: CTAS: Create a Table of Active Members**  
-- Use the CREATE TABLE AS (CTAS) statement to create a new table active_members containing members who have issued
 at least one book in the last 2 months.
```sql
CREATE TABLE active_members
AS
SELECT * FROM members
WHERE member_id IN (
						SELECT 
							DISTINCT issued_member_id
						FROM issued_status
						WHERE DATEDIFF('2024-05-30', issued_date) < 60
					);
```

**Task 16: Find Employees with the Most Book Issues Processed**
Write a query to find the top 3 employees who have processed the most book issues. Display the employee name, 
number of books processed, and their branch.
```sql
SELECT 
	e.emp_id,
	e.emp_name,
    COUNT(ist.issued_id) AS books_processed,
    b.branch_id,
    b.branch_address
FROM employees AS e
JOIN issued_status AS ist
ON e.emp_id = ist.issued_emp_id
JOIN branch AS b
ON e.branch_id = b.branch_id
GROUP BY 1,4,5
ORDER BY 3 DESC
LIMIT 3;
```
