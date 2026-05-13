# CREATE TABLES

```sql id="p35bhg"
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

```sql id="8b03f4"
-- =========================
-- INSERT INTO PROJECT
-- =========================

INSERT INTO Project VALUES
(101,'AI','Software',12),

(102,'Banking System','Finance',10),

(103,'E-Commerce','Web',8);

-- =========================
-- INSERT INTO EMPLOYEE
-- =========================

INSERT INTO Employee VALUES
(1,'Raj','M.Sc','2020-06-10'),

(2,'Pooja','BCA','2021-01-15'),

(3,'Amit','M.Sc','2019-03-20'),

(4,'Sneha','MCA','2022-07-11');

-- =========================
-- INSERT INTO PROJECT_EMPLOYEE
-- =========================

INSERT INTO Project_Employee VALUES
(101,1,'2024-01-10',120),

(101,2,'2024-01-15',100),

(102,3,'2024-02-01',90),

(103,4,'2024-03-05',80),

(101,3,'2024-01-20',110);
```

---

# Q1 SOLUTION

# 1) Display project name, project type and project start date

```sql id="v83v2f"
SELECT p.pname,
       p.ptype,
       pe.start_date_of_project
FROM Project p
JOIN Project_Employee pe
ON p.Pno = pe.Pno;
```

---

# 2) Display details of employees working on 'AI' project

```sql id="6wjqel"
SELECT e.*
FROM Employee e
JOIN Project_Employee pe
ON e.Eno = pe.Eno
JOIN Project p
ON pe.Pno = p.Pno
WHERE p.pname = 'AI';
```

---

# 3) List name of employee whose qualification is 'M.Sc'

```sql id="jlwmq4"
SELECT ename
FROM Employee
WHERE qualification = 'M.Sc';
```

---

# 4) Count the number of employee

```sql id="7h7z81"
SELECT COUNT(*) AS total_employee
FROM Employee;
```

---

# Q2 SOLUTION

# 1) Trigger before inserting duration into Project table

```sql id="j9oqvx"
-- =========================
-- TRIGGER FUNCTION
-- =========================

CREATE OR REPLACE FUNCTION check_duration()
RETURNS TRIGGER AS
$$
BEGIN

    IF NEW.duration <= 0 THEN
        RAISE EXCEPTION 'Duration must be greater than zero';
    END IF;

    RETURN NEW;

END;
$$
LANGUAGE plpgsql;
```

---

# CREATE TRIGGER

```sql id="fwzwn4"
CREATE TRIGGER trg_check_duration
BEFORE INSERT
ON Project
FOR EACH ROW
EXECUTE FUNCTION check_duration();
```

---

# TEST TRIGGER

```sql id="59lcqu"
INSERT INTO Project VALUES
(104,'Testing','Software',-5);
```

---

# 2) Function to accept project name and display employee names

```sql id="7hmty0"
CREATE OR REPLACE FUNCTION employee_project(p_project_name VARCHAR)
RETURNS TABLE (
    employee_name VARCHAR
)
AS
$$
BEGIN

    RETURN QUERY

    SELECT e.ename
    FROM Employee e
    JOIN Project_Employee pe
    ON e.Eno = pe.Eno
    JOIN Project p
    ON pe.Pno = p.Pno
    WHERE p.pname = p_project_name;

END;
$$
LANGUAGE plpgsql;
```

---

# EXECUTE FUNCTION

```sql id="7fh0nz"
SELECT * FROM employee_project('AI');
```
