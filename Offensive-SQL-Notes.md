
# 🗄️ SQL Cheat Sheet

This document contains basic **SQL commands for database interaction**, including:

- Retrieving data
- Updating data
- Deleting data
- Creating databases and tables
- Managing database users
- Assigning permissions

---

# 📥 Retrieving Data

## Get All Data

```sql
SELECT * FROM users;
````

---

## Get Specific Columns

```sql
SELECT id, name, email FROM users;
```

---

## Retrieve Data with Condition

```sql
SELECT * FROM users
WHERE age > 18;
```

---

## Multiple Conditions

```sql
SELECT * FROM users
WHERE age > 18 AND country = 'Morocco';
```

---

## Sort Results

```sql
SELECT * FROM users
ORDER BY age DESC;
```

`DESC` = descending order
`ASC` = ascending order

---

## Limit Results

```sql
SELECT * FROM users
LIMIT 10;
```

Useful for **pagination and large datasets**.

---

# ✏️ Updating Data

## Update One Column

```sql
UPDATE users
SET email = 'new@mail.com'
WHERE id = 5;
```

---

## Update Multiple Columns

```sql
UPDATE users
SET name = 'Mustapha',
    age = 20
WHERE id = 5;
```

⚠️ **Important:** Always use a `WHERE` clause.

Without it:

```sql
UPDATE users SET email = 'new@mail.com';
```

You will update **every row in the table**.

---

# 🗑️ Deleting Data

## Delete a Specific Row

```sql
DELETE FROM users
WHERE id = 5;
```

---

## Delete with Condition

```sql
DELETE FROM users
WHERE age < 18;
```

---

## Delete All Rows (Table Still Exists)

```sql
DELETE FROM users;
```

---

## Faster Full Delete (Dangerous)

```sql
TRUNCATE TABLE users;
```

Differences:

| Command  | Effect                          |
| -------- | ------------------------------- |
| DELETE   | Removes rows one by one         |
| TRUNCATE | Quickly clears the entire table |

---

# 🏗️ Creating Databases and Tables

## Create a Database

```sql
CREATE DATABASE my_database;
```

---

## Select Database

```sql
USE my_database;
```

---

## Create a Table

```sql
CREATE TABLE users (
    id INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(100),
    email VARCHAR(150) UNIQUE,
    age INT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

Explanation:

| Column     | Description          |
| ---------- | -------------------- |
| id         | Unique identifier    |
| name       | User name            |
| email      | Unique email address |
| age        | Integer value        |
| created_at | Auto timestamp       |

---

# 👤 Managing Database Users

## Create a Database User

```sql
CREATE USER 'appuser'@'localhost'
IDENTIFIED BY 'strong_password';
```

---

## Remove a User

```sql
DROP USER 'appuser'@'localhost';
```

---

## Change User Password

```sql
ALTER USER 'appuser'@'localhost'
IDENTIFIED BY 'new_password';
```

---

# 🔐 Assigning Permissions

## Grant Full Access to a Database

```sql
GRANT ALL PRIVILEGES
ON my_database.*
TO 'appuser'@'localhost';
```

---

## Grant Limited Permissions

```sql
GRANT SELECT, INSERT, UPDATE
ON my_database.users
TO 'appuser'@'localhost';
```

---

## Apply Privilege Changes

```sql
FLUSH PRIVILEGES;
```

---

## Remove Permissions

```sql
REVOKE INSERT, UPDATE
ON my_database.users
FROM 'appuser'@'localhost';
```

---

# ⚡ Summary

Common SQL operations used in databases:

| Operation          | Command                    |
| ------------------ | -------------------------- |
| Retrieve data      | `SELECT`                   |
| Update data        | `UPDATE`                   |
| Delete data        | `DELETE`                   |
| Create tables      | `CREATE TABLE`             |
| Manage users       | `CREATE USER`, `DROP USER` |
| Manage permissions | `GRANT`, `REVOKE`          |

---

# 🧠 Practical Usage

These SQL commands are essential for:

* Backend development
* Database administration
* Web application security testing
* SQL injection exploitation analysis
* Capture The Flag (CTF) challenges

