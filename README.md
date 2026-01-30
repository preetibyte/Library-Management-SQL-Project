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
