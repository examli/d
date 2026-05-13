# CREATE TABLES

```sql id="ot7r3m"
-- =========================
-- DEPARTMENT TABLE
-- =========================

CREATE TABLE Department (
    deptid INT PRIMARY KEY,
    dname VARCHAR(30),
    location VARCHAR(30)
);

-- =========================
-- EMPLOYEE TABLE
-- =========================

CREATE TABLE Employee (
    empid INT PRIMARY KEY,
    ename VARCHAR(30),
    phone VARCHAR(15),
    birthdate DATE,
    deptid INT,

    FOREIGN KEY (deptid)
    REFERENCES Department(deptid)
);
```

---

# INSERT RECORDS

```sql id="v7n4qx"
-- =========================
-- INSERT INTO DEPARTMENT
-- =========================

INSERT INTO Department VALUES
(101,'Computer Science','Pune'),

(102,'Mechanical','Mumbai'),

(103,'Electronics','Nashik');

-- =========================
-- INSERT INTO EMPLOYEE
-- =========================

INSERT INTO Employee VALUES
(1,'Sachin','9876543210','1998-05-10',101),

(2,'Riya','9988776655','1898-08-12',102),

(3,'Sneha','9123456789','1997-01-15',101),

(4,'Amit','9876501234','2000-02-20',103);
```

---

# Q1 SOLUTION

# 1) List employees in 'Computer Science' department

```sql id="ep3w9k"
SELECT e.ename
FROM Employee e
JOIN Department d
ON e.deptid = d.deptid
WHERE d.dname = 'Computer Science';
```

---

# 2) Delete employee whose name starts with 'S'

```sql id="7u8w9r"
DELETE FROM Employee
WHERE ename LIKE 'S%';
```

---

# 3) Insert 2 records into Department

```sql id="6wz1oy"
INSERT INTO Department VALUES
(104,'Civil','Satara'),

(105,'IT','Kolhapur');
```

---

# 4) Display employee name along with department

```sql id="yb5m1f"
SELECT e.ename,
       d.dname
FROM Employee e
JOIN Department d
ON e.deptid = d.deptid;
```

---

# Q2 SOLUTION

# 1) Create Views

## (a) Display employee details having birth date before 1900

```sql id="z0pb2m"
CREATE VIEW employee_before_1900 AS
SELECT *
FROM Employee
WHERE birthdate < '1900-01-01';
```

---

## DISPLAY VIEW

```sql id="r2wq8f"
SELECT * FROM employee_before_1900;
```

---

## (b) Display employee name with phone number

```sql id="e4m9tx"
CREATE VIEW employee_phone_view AS
SELECT ename,
       phone
FROM Employee;
```

---

## DISPLAY VIEW

```sql id="q1o7nc"
SELECT * FROM employee_phone_view;
```

---

# 2) Cursor to display employee details with department name

```sql id="3xg4lp"
CREATE OR REPLACE FUNCTION employee_department_cursor()
RETURNS VOID AS
$$
DECLARE

    rec RECORD;

    cur CURSOR FOR
    SELECT e.empid,
           e.ename,
           d.dname
    FROM Employee e
    JOIN Department d
    ON e.deptid = d.deptid;

BEGIN

    OPEN cur;

    LOOP

        FETCH cur INTO rec;

        EXIT WHEN NOT FOUND;

        RAISE NOTICE 'Employee ID: %, Name: %, Department: %',
        rec.empid,
        rec.ename,
        rec.dname;

    END LOOP;

    CLOSE cur;

END;
$$
LANGUAGE plpgsql;
```

---

# EXECUTE FUNCTION

```sql id="d2s9ef"
SELECT employee_department_cursor();
```
