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
-- Branch Table
CREATE TABLE Branch (
    Bid INTEGER PRIMARY KEY,
    brname VARCHAR(30),
    brcity VARCHAR(10)
);

-- Customer Table
CREATE TABLE Customer (
    Cno INTEGER PRIMARY KEY,
    cname VARCHAR(20),
    caddr VARCHAR(35),
    city VARCHAR(15)
);

-- Loan Application Table
CREATE TABLE Loan_application (
    Lno INTEGER PRIMARY KEY,
    l_amt_required MONEY CHECK (l_amt_required > 0::money),
    lamtapproved MONEY,
    l_date DATE
);

-- Relationship Table
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

Insert Values
```sql
INSERT INTO Branch VALUES
(1, 'Aundh', 'Pune'),
(2, 'ShivajiNagar', 'Pune'),
(3, 'Hadapsar', 'Pune');

INSERT INTO Customer VALUES
(101, 'Amit', 'Street 1', 'Pune'),
(102, 'Riya', 'Street 2', 'Mumbai'),
(103, 'John', 'Street 3', 'Delhi');

INSERT INTO Loan_application VALUES
(201, 50000, 45000, '2019-06-10'),
(202, 70000, 65000, '2019-12-15'),
(203, 30000, 25000, '2020-03-20'),
(204, 90000, 85000, '2020-05-25');

INSERT INTO Ternary VALUES
(1, 101, 201),
(1, 102, 202),
(2, 103, 203),
(1, 103, 204);
```
1. Display sum of loan amount approved branch wise from 1st June 2019 to 1st June 2020.
```sql
SELECT b.brname, SUM(l.lamtapproved)
FROM Branch b
JOIN Ternary t ON b.Bid = t.Bid
JOIN Loan_application l ON t.Lno = l.Lno
WHERE l.l_date BETWEEN '2019-06-01' AND '2020-06-01'
GROUP BY b.brname;
```
2. Display  customers details of  ‘Aundh’ branch. 
```sql
SELECT c.*
FROM Customer c
JOIN Ternary t ON c.Cno = t.Cno
JOIN Branch b ON t.Bid = b.Bid
WHERE b.brname = 'Aundh';
```
3. Display total of loan amount required. 
```sql
SELECT SUM(l_amt_required)
FROM Loan_application;
```
4. Display all details of Branch table. 
```sql
SELECT * FROM Branch;
```
1. Write a trigger before insert record of customer. If the customer number is less than or 
equal to zero and customer name is null then give the appropriate message 
```sql
CREATE OR REPLACE FUNCTION check_customer()
RETURNS TRIGGER AS $$
BEGIN
    IF NEW.Cno <= 0 AND NEW.cname IS NULL THEN
        RAISE EXCEPTION 'Invalid Customer Data';
    END IF;
    RETURN NEW;
END;
$$ LANGUAGE plpgsql;

CREATE TRIGGER trg_customer_check
BEFORE INSERT ON Customer
FOR EACH ROW
EXECUTE FUNCTION check_customer();
```
2. Write a stored function to accept customer name as an input parameter and display 
loan information of that customer. 
```sql
CREATE OR REPLACE FUNCTION get_loan_info(cust_name VARCHAR)
RETURNS TABLE (
    cname VARCHAR,
    loan_no INTEGER,
    amount_required MONEY,
    amount_approved MONEY,
    loan_date DATE
) AS $$
BEGIN
    RETURN QUERY
    SELECT c.cname, l.Lno, l.l_amt_required, l.lamtapproved, l.l_date
    FROM Customer c
    JOIN Ternary t ON c.Cno = t.Cno
    JOIN Loan_application l ON t.Lno = l.Lno
    WHERE c.cname = cust_name;
END;
$$ LANGUAGE plpgsql;
```
