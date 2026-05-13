# CREATE TABLES

```sql id="4x8iq0"
-- =========================
-- STUDENT TABLE
-- =========================

CREATE TABLE Student (
    rollno INT PRIMARY KEY,
    sname VARCHAR(30),
    phone VARCHAR(15)
);

-- =========================
-- COURSE TABLE
-- =========================

CREATE TABLE Course (
    ccode INT PRIMARY KEY,
    cname VARCHAR(30),
    duration VARCHAR(20)
);

-- =========================
-- STUDENT_COURSE TABLE
-- =========================

CREATE TABLE Student_Course (
    rollno INT,
    ccode INT,

    PRIMARY KEY (rollno, ccode),

    FOREIGN KEY (rollno) REFERENCES Student(rollno),

    FOREIGN KEY (ccode) REFERENCES Course(ccode)
);
```

---

# INSERT RECORDS

```sql id="t7qg6h"
-- =========================
-- INSERT INTO STUDENT
-- =========================

INSERT INTO Student VALUES
(1,'Rohan','9876543210'),

(2,'Riya','9123456789'),

(3,'Amit','9988776655'),

(4,'Sneha','9876501234');

-- =========================
-- INSERT INTO COURSE
-- =========================

INSERT INTO Course VALUES
(101,'Python','6 months'),

(102,'Java','12 months'),

(103,'DBMS','6 months'),

(104,'Web Development','4 months');

-- =========================
-- INSERT INTO STUDENT_COURSE
-- =========================

INSERT INTO Student_Course VALUES
(1,101),

(1,102),

(2,103),

(3,101),

(4,104);
```

---

# Q1 SOLUTION

# 1) List details of courses with duration '6 months'

```sql id="j6p2h5"
SELECT *
FROM Course
WHERE duration = '6 months';
```

---

# 2) List student-wise course details

```sql id="ffncf4"
SELECT s.sname,
       c.cname,
       c.duration
FROM Student s
JOIN Student_Course sc
ON s.rollno = sc.rollno
JOIN Course c
ON sc.ccode = c.ccode;
```

---

# 3) Delete the student record of 'Rohan'

```sql id="qohlyz"
DELETE FROM Student
WHERE sname = 'Rohan';
```

---

# 4) Update phone number of 'Riya' to 987654321

```sql id="hm25o3"
UPDATE Student
SET phone = '987654321'
WHERE sname = 'Riya';
```

---

# Q2 SOLUTION

# 1) Trigger before insert record of Student

```sql id="yppx5q"
-- =========================
-- TRIGGER FUNCTION
-- =========================

CREATE OR REPLACE FUNCTION check_rollno()
RETURNS TRIGGER AS
$$
BEGIN

    IF NEW.rollno <= 0 THEN
        RAISE EXCEPTION 'Roll number must be greater than zero';
    END IF;

    RETURN NEW;

END;
$$
LANGUAGE plpgsql;
```

---

# CREATE TRIGGER

```sql id="lbtoed"
CREATE TRIGGER trg_check_rollno
BEFORE INSERT
ON Student
FOR EACH ROW
EXECUTE FUNCTION check_rollno();
```

---

# TEST TRIGGER

```sql id="6h8s3n"
INSERT INTO Student VALUES
(-1,'Test','9999999999');
```

---

# 2) Stored Function to accept course code and display course name with duration

```sql id="59x4u6"
CREATE OR REPLACE FUNCTION get_course_details(code INT)
RETURNS TABLE (
    course_name VARCHAR,
    course_duration VARCHAR
)
AS
$$
BEGIN

    RETURN QUERY

    SELECT cname,
           duration
    FROM Course
    WHERE ccode = code;

END;
$$
LANGUAGE plpgsql;
```

---

# EXECUTE FUNCTION

```sql id="qdrk0o"
SELECT * FROM get_course_details(101);
```
