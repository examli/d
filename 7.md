# CREATE TABLES

```sql
-- =========================
-- TRAIN TABLE
-- =========================

CREATE TABLE Train (
    Train_no INT PRIMARY KEY,
    train_name VARCHAR(20),
    depart_time TIME,
    arrival_time TIME,
    source_stn VARCHAR(20),
    dest_stn VARCHAR(20),
    no_of_res_bogies INT,
    bogie_capacity INT
);

-- =========================
-- PASSENGER TABLE
-- =========================

CREATE TABLE Passenger (
    Passenger_id INT PRIMARY KEY,
    passenger_name VARCHAR(20),
    address VARCHAR(30),
    age INT,
    gender CHAR(1)
);

-- =========================
-- TICKET TABLE
-- =========================

CREATE TABLE Ticket (
    Ticket_no INT PRIMARY KEY,
    Train_no INT,
    Passenger_id INT,
    bogie_no INT,
    no_of_berths INT,
    tdate DATE,
    ticket_amt DECIMAL(7,2),
    ticket_status CHAR(1),

    FOREIGN KEY (Train_no) REFERENCES Train(Train_no),
    FOREIGN KEY (Passenger_id) REFERENCES Passenger(Passenger_id),

    CHECK (ticket_status IN ('W','C'))
);
```

---

# INSERT RECORDS

```sql
-- =========================
-- INSERT INTO TRAIN
-- =========================

INSERT INTO Train VALUES
(101,'Shatabdi Express','08:00','14:00','Pune','Mumbai',10,72),

(102,'Rajdhani Express','09:00','18:00','Delhi','Mumbai',12,80),

(103,'Deccan Queen','07:00','11:00','Pune','Mumbai',8,70);

-- =========================
-- INSERT INTO PASSENGER
-- =========================

INSERT INTO Passenger VALUES
(1,'MrRaj','Pune',25,'M'),

(2,'Pooja','Mumbai',22,'F'),

(3,'Rahul','Nashik',30,'M'),

(4,'Sneha','Pune',19,'F');

-- =========================
-- INSERT INTO TICKET
-- =========================

INSERT INTO Ticket VALUES
(1001,101,1,2,2,'2024-04-10',1500.00,'C'),

(1002,101,2,3,1,'2024-04-10',1500.00,'C'),

(1003,102,3,1,2,'2024-04-11',2500.00,'W');
```

---

# Q1 SOLUTION

## 1) Display names of 'Shatabdi Express' passengers

```sql
SELECT p.passenger_name
FROM Passenger p
JOIN Ticket t
ON p.Passenger_id = t.Passenger_id
JOIN Train tr
ON t.Train_no = tr.Train_no
WHERE tr.train_name = 'Shatabdi Express';
```

---

## 2) Insert 2 records into Ticket table

```sql
INSERT INTO Ticket VALUES
(1004,103,4,2,1,'2024-04-12',1200.00,'C'),

(1005,102,2,4,2,'2024-04-13',2300.00,'W');
```

---

## 3) List the Train Details

```sql
SELECT *
FROM Train;
```

---

## 4) List Ticket detail of 'MrRaj'

```sql
SELECT t.*
FROM Ticket t
JOIN Passenger p
ON t.Passenger_id = p.Passenger_id
WHERE p.passenger_name = 'MrRaj';
```

---

# Q2 SOLUTION

# 1) Trigger after insert on Passenger

```sql
-- =========================
-- TRIGGER FUNCTION
-- =========================

CREATE OR REPLACE FUNCTION check_age()
RETURNS TRIGGER AS
$$
BEGIN

    IF NEW.age > 5 THEN
        RAISE NOTICE 'Age above 5 will be charged full fare';
    END IF;

    RETURN NEW;

END;
$$
LANGUAGE plpgsql;
```

---

## CREATE TRIGGER

```sql
CREATE TRIGGER trg_check_age
AFTER INSERT
ON Passenger
FOR EACH ROW
EXECUTE FUNCTION check_age();
```

---

## TEST TRIGGER

```sql
INSERT INTO Passenger VALUES
(5,'Aman','Pune',10,'M');
```

---

# 2) Stored Function using Cursor

```sql
CREATE OR REPLACE FUNCTION total_berths(d DATE)
RETURNS INT AS
$$
DECLARE

    total INT := 0;

    b INT;

    cur CURSOR FOR
    SELECT no_of_berths
    FROM Ticket
    WHERE tdate = d;

BEGIN

    OPEN cur;

    LOOP

        FETCH cur INTO b;

        EXIT WHEN NOT FOUND;

        total := total + b;

    END LOOP;

    CLOSE cur;

    RETURN total;

END;
$$
LANGUAGE plpgsql;
```

---

# EXECUTE FUNCTION

```sql
SELECT total_berths('2024-04-10');
```
