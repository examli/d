Create Database
```sql
CREATE DATABASE bank_db;
```
Connect to the Database
```sql
\c bank_db;
```
Create Tables
```sql
-- Branch
CREATE TABLE Branch (
    Bid INTEGER PRIMARY KEY,
    brname VARCHAR(30),
    brcity VARCHAR(10)
);

-- Customer
CREATE TABLE Customer (
    Cno INTEGER PRIMARY KEY,
    cname VARCHAR(20),
    caddr VARCHAR(35),
    city VARCHAR(15)
);

-- Loan Application
CREATE TABLE Loan_application (
    Lno INTEGER PRIMARY KEY,
    l_amt_required MONEY CHECK (l_amt_required > 0),
    lamtapproved MONEY,
    l_date DATE
);

-- Ternary Relationship
CREATE TABLE Ternary (
    Bid INTEGER,
    Cno INTEGER,
    Lno INTEGER,
    PRIMARY KEY (Bid, Cno, Lno),
    FOREIGN KEY (Bid) REFERENCES Branch(Bid),
    FOREIGN KEY (Cno) REFERENCES Customer(Cno),
    FOREIGN KEY (Lno) REFERENCES Loan_application(Lno)
);
```
Insert five records into Branch
```sql
INSERT INTO Branch VALUES
(1, 'Pimpri', 'Pune'),
(2, 'Aundh', 'Pune'),
(3, 'ShivajiNagar', 'Pune'),
(4, 'Hadapsar', 'Pune'),
(5, 'Kothrud', 'Pune');
```
Insert Customer
```sql
INSERT INTO Customer VALUES
(101, 'Amit', 'Addr1', 'Pune'),
(102, 'Anita', 'Addr2', 'Mumbai'),
(103, 'Rohit', 'Addr3', 'Delhi'),
(104, 'Ajay', 'Addr4', 'Pune'),
(105, 'Sita', 'Addr5', 'Mumbai');
```
Insert Loan Application
```sql
INSERT INTO Loan_application VALUES
(201, 800000, 750000, '2020-01-10'),
(202, 500000, 450000, '2020-02-15'),
(203, 800000, 700000, '2020-03-20'),
(204, 300000, 250000, '2020-04-25'),
(205, 800000, 780000, '2020-05-30');
```
Insert Ternary Relationship
```sql
INSERT INTO Ternary VALUES
(1, 101, 201),
(1, 102, 202),
(2, 103, 203),
(1, 104, 204),
(3, 105, 205);
```
2. Display Loan Detais for Pimpri Branch
```sql
SELECT l.*
FROM Loan_application l
JOIN Ternary t ON l.Lno = t.Lno
JOIN Branch b ON t.Bid = b.Bid
WHERE b.brname = 'Pimpri';
```

3. Display name of customers whose name starts with 'A'
```sql
SELECT cname
FROM Customer
WHERE cname LIKE 'A%';
```
4. Customers who applied for loan of 8,00,000
```sql
SELECT c.*
FROM Customer c
JOIN Ternary t ON c.Cno = t.Cno
JOIN Loan_application l ON t.Lno = l.Lno
WHERE l.l_amt_required = 800000;
```

1. Create VIEW for Pimpri branch customers
```sql
CREATE VIEW Pimpri_Customers AS
SELECT c.*
FROM Customer c
JOIN Ternary t ON c.Cno = t.Cno
JOIN Branch b ON t.Bid = b.Bid
WHERE b.brname = 'Pimpri';
```

2. Stored Function: Count customers of a branch
```sql
CREATE OR REPLACE FUNCTION count_customers(branch_name VARCHAR)
RETURNS TEXT AS $$
DECLARE
    total INTEGER;
BEGIN
    SELECT COUNT(*)
    INTO total
    FROM Customer c
    JOIN Ternary t ON c.Cno = t.Cno
    JOIN Branch b ON t.Bid = b.Bid
    WHERE b.brname = branch_name;

    IF total = 0 THEN
        RETURN 'Invalid branch name or no customers found';
    ELSE
        RETURN 'Total Customers: ' || total;
    END IF;
END;
$$ LANGUAGE plpgsql;
```

Call Function
```sql
SELECT count_customers('Pimpri');
```
