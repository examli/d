Create Database
```sql
CREATE DATABASE student_db;
```

Connect to Database
```sql
\c student_db;
```

Create Tables
```sql
-- Student Table
CREATE TABLE Student (
    Sid INTEGER PRIMARY KEY,
    sname VARCHAR(30),
    class VARCHAR(20)
);

-- Competition Table
CREATE TABLE Competition (
    cno INTEGER PRIMARY KEY,
    cname VARCHAR(30),
    ctype VARCHAR(20)
);

-- Relationship Table (Many-to-Many with attributes)
CREATE TABLE Student_Competition (
    Sid INTEGER,
    cno INTEGER,
    rank INTEGER,
    year INTEGER,
    PRIMARY KEY (Sid, cno),
    FOREIGN KEY (Sid) REFERENCES Student(Sid),
    FOREIGN KEY (cno) REFERENCES Competition(cno)
);
```

Insert Data (Sufficient Records)
```sql
-- Student
INSERT INTO Student VALUES
(1, 'Amit', 'FY'),
(2, 'Riya', 'SY'),
(3, 'Ajay', 'TY'),
(4, 'Neha', 'FY'),
(5, 'Anita', 'SY');

-- Competition
INSERT INTO Competition VALUES
(101, 'Coding Contest', 'National'),
(102, 'Quiz', 'State'),
(103, 'Hackathon', 'National'),
(104, 'Debate', 'College');

-- Student_Competition
INSERT INTO Student_Competition VALUES
(1, 101, 1, 2023),
(2, 102, 2, 2023),
(3, 103, 3, 2024),
(4, 101, 2, 2024),
(5, 103, 1, 2023);
```

1. Display Student id and name
```sql
SELECT Sid, sname FROM Student;
```

2. List student names in sorting order
```sql
SELECT sname FROM Student
ORDER BY sname ASC;
```

3. Students who took part in National level competition
```sql
SELECT DISTINCT s.sname
FROM Student s
JOIN Student_Competition sc ON s.Sid = sc.Sid
JOIN Competition c ON sc.cno = c.cno
WHERE c.ctype = 'National';
```

4. List competition details
```sql
SELECT * FROM Competition;
```


1. Trigger before insert on Student

Condition: Sid <= 0 → error
```sql
CREATE OR REPLACE FUNCTION check_student()
RETURNS TRIGGER AS $$
BEGIN
    IF NEW.Sid <= 0 THEN
        RAISE EXCEPTION 'Student ID must be greater than zero';
    END IF;
    RETURN NEW;
END;
$$ LANGUAGE plpgsql;

CREATE TRIGGER trg_student_check
BEFORE INSERT ON Student
FOR EACH ROW
EXECUTE FUNCTION check_student();
```

2. Create VIEW (Student details with Competition name)
```sql
CREATE VIEW Student_Competition_View AS
SELECT s.Sid, s.sname, s.class, c.cname
FROM Student s
JOIN Student_Competition sc ON s.Sid = sc.Sid
JOIN Competition c ON sc.cno = c.cno;
```

View Usage
```sql
SELECT * FROM Student_Competition_View;
```
