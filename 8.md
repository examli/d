# CREATE TABLES

```sql id="c75hgo"
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

```sql id="wf2zwu"
-- =========================
-- INSERT INTO TRAIN
-- =========================

INSERT INTO Train VALUES
(101,'Koyana Express','08:00','14:00','Pune','Kolhapur',10,72),

(102,'Rajdhani Express','09:00','18:00','Delhi','Mumbai',12,80),

(103,'Deccan Queen','07:00','11:00','Pune','Mumbai',8,70);

-- =========================
-- INSERT INTO PASSENGER
-- =========================

INSERT INTO Passenger VALUES
(1,'Raj','Pune',25,'M'),

(2,'Pooja','Mumbai',22,'F'),

(3,'Aman','Nashik',4,'M'),

(4,'Sneha','Pune',19,'F'),

(5,'Riya','Satara',3,'F');

-- =========================
-- INSERT INTO TICKET
-- =========================

INSERT INTO Ticket VALUES
(1001,101,1,2,2,'2021-04-12',1500.00,'C'),

(1002,101,2,3,1,'2021-04-12',1500.00,'W'),

(1003,102,3,1,2,'2021-04-12',2500.00,'C'),

(1004,102,4,2,1,'2021-04-12',2500.00,'W'),

(1005,103,5,1,1,'2021-04-13',1200.00,'C');
```

---

# Q1 SOLUTION

# 1) Display names of 'Koyana Express' passengers

```sql id="o0iy1j"
SELECT p.passenger_name
FROM Passenger p
JOIN Ticket t
ON p.Passenger_id = t.Passenger_id
JOIN Train tr
ON t.Train_no = tr.Train_no
WHERE tr.train_name = 'Koyana Express';
```

---

# 2) Display count of confirmed bookings of 'Rajdhani Express'

```sql id="4brl5z"
SELECT COUNT(*) AS confirmed_bookings
FROM Ticket t
JOIN Train tr
ON t.Train_no = tr.Train_no
WHERE tr.train_name = 'Rajdhani Express'
AND t.ticket_status = 'C';
```

---

# 3) Sort the Passenger details as per age

```sql id="jlwmx7"
SELECT *
FROM Passenger
ORDER BY age;
```

---

# 4) Display name of passenger whose age < 5

```sql id="p5n0pq"
SELECT passenger_name
FROM Passenger
WHERE age < 5;
```

---

# Q2 SOLUTION

# 1) Create a view to display passenger details whose status is confirmed

```sql id="6v04uo"
CREATE VIEW confirmed_passenger AS
SELECT p.*, t.ticket_status
FROM Passenger p
JOIN Ticket t
ON p.Passenger_id = t.Passenger_id
WHERE t.ticket_status = 'C';
```

---

# DISPLAY VIEW

```sql id="djkhnf"
SELECT * FROM confirmed_passenger;
```

---

# 2) Stored Function to display train wise bookings on 12-04-2021 whose ticket status is waiting

```sql id="t8cw3m"
CREATE OR REPLACE FUNCTION waiting_bookings()
RETURNS TABLE (
    train_name VARCHAR,
    total_waiting BIGINT
)
AS
$$
BEGIN

    RETURN QUERY

    SELECT
        tr.train_name,
        COUNT(t.Ticket_no)

    FROM Train tr
    JOIN Ticket t
    ON tr.Train_no = t.Train_no

    WHERE t.tdate = '2021-04-12'
    AND t.ticket_status = 'W'

    GROUP BY tr.train_name;

END;
$$
LANGUAGE plpgsql;
```

---

# EXECUTE FUNCTION

```sql id="f4s06u"
SELECT * FROM waiting_bookings();
```
