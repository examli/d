# CREATE TABLES

```sql id="ddmb6z"
-- =========================
-- PROJECT TABLE
-- =========================

CREATE TABLE Project (
    Pno INT PRIMARY KEY,
    pname VARCHAR(30) NOT NULL,
    ptype VARCHAR(20),
    duration INT
);

-- =========================
-- EMPLOYEE TABLE
-- =========================

CREATE TABLE Employee (
    Eno INT PRIMARY KEY,
    ename VARCHAR(20),
    qualification VARCHAR(15),
    joining_date DATE
);

-- =========================
-- PROJECT_EMPLOYEE TABLE
-- =========================

CREATE TABLE Project_Employee (
    Pno INT,
    Eno INT,
    start_date_of_project DATE,
    no_of_hours_worked INT,

    PRIMARY KEY (Pno, Eno),

    FOREIGN KEY (Pno) REFERENCES Project(Pno),

    FOREIGN KEY (Eno) REFERENCES Employee(Eno)
);
```

---

# INSERT RECORDS

```sql id="p01evf"
-- =========================
-- INSERT INTO PROJECT
-- =========================

INSERT INTO Project VALUES
(101,'AI','Software',5),

(102,'Banking System','Finance',8),

(103,'E-Commerce','Web',4);

-- =========================
-- INSERT INTO EMPLOYEE
-- =========================

INSERT INTO Employee VALUES
(1,'Rahul','MCA','2019-06-10'),

(2,'Poojal','BCA','2020-01-15'),

(3,'Amit','M.Sc','2018-03-20'),

(4,'Snehal','MCA','2021-07-11');

-- =========================
-- INSERT INTO PROJECT_EMPLOYEE
-- =========================

INSERT INTO Project_Employee VALUES
(101,1,'2017-09-20',120),

(102,2,'2018-01-15',100),

(103,3,'2017-09-20',90),

(101,4,'2019-03-05',80);
```

---

# Q1 SOLUTION

# 1) List the name of employee whose name ends by letter 'L'

```sql id="sv5vxm"
SELECT ename
FROM Employee
WHERE ename LIKE '%l';
```

---

# 2) Insert 2 records into Employee table

```sql id="l9r4qw"
INSERT INTO Employee VALUES
(5,'Kunal','BCA','2022-02-10'),

(6,'Komal','MCA','2023-01-12');
```

---

# 3) Display Project details which start on date '20-09-2017'

```sql id="0xv4t1"
SELECT p.*
FROM Project p
JOIN Project_Employee pe
ON p.Pno = pe.Pno
WHERE pe.start_date_of_project = '2017-09-20';
```

---

# 4) Display Project details whose duration > 3

```sql id="6owkdo"
SELECT *
FROM Project
WHERE duration > 3;
```

---

# Q2 SOLUTION

# 1) Trigger before deleting employee record

```sql id="drvyfx"
-- =========================
-- TRIGGER FUNCTION
-- =========================

CREATE OR REPLACE FUNCTION employee_delete_msg()
RETURNS TRIGGER AS
$$
BEGIN

    RAISE NOTICE 'Employee record is being deleted';

    RETURN OLD;

END;
$$
LANGUAGE plpgsql;
```

---

# CREATE TRIGGER

```sql id="yq5v9j"
CREATE TRIGGER trg_employee_delete
BEFORE DELETE
ON Employee
FOR EACH ROW
EXECUTE FUNCTION employee_delete_msg();
```

---

# TEST TRIGGER

```sql id="2xwfrs"
DELETE FROM Employee
WHERE Eno = 1;
```

---

# 2) Create View to display employee details sorted by name descending

```sql id="5mztt7"
CREATE VIEW employee_desc_view AS
SELECT *
FROM Employee
ORDER BY ename DESC;
```

---

# DISPLAY VIEW

```sql id="v7s5g2"
SELECT * FROM employee_desc_view;
```
