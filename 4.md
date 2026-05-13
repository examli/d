Create Database
```sql
CREATE DATABASE transport_db;
```

Connect to Database
```sql
\c transport_db;
```

Create Tables
```sql
-- Bus Table
CREATE TABLE Bus (
    Bus_no INTEGER PRIMARY KEY,
    capacity INTEGER NOT NULL,
    depot_name VARCHAR(20)
);

-- Route Table
CREATE TABLE Route (
    Route_no INTEGER PRIMARY KEY,
    source VARCHAR(20),
    destination VARCHAR(20),
    no_of_stations INTEGER
);

-- Driver Table
CREATE TABLE Driver (
    Driver_no INTEGER PRIMARY KEY,
    driver_name VARCHAR(20),
    license_no VARCHAR(20) UNIQUE,
    address VARCHAR(20),
    age INTEGER,
    salary FLOAT
);

-- Relationship Table (Many-to-Many with attributes)
CREATE TABLE Bus_Route_Driver (
    Bus_no INTEGER,
    Route_no INTEGER,
    Driver_no INTEGER,
    shift INTEGER CHECK (shift IN (1,2)), -- 1 Morning, 2 Evening
    date_of_duty DATE,
    PRIMARY KEY (Bus_no, Route_no, Driver_no),
    FOREIGN KEY (Bus_no) REFERENCES Bus(Bus_no),
    FOREIGN KEY (Route_no) REFERENCES Route(Route_no),
    FOREIGN KEY (Driver_no) REFERENCES Driver(Driver_no)
);
```


Insert Data (Sufficient Records)
```sql
-- Bus
INSERT INTO Bus VALUES
(10, 50, 'DepotA'),
(11, 45, 'DepotB'),
(12, 60, 'DepotC');

-- Route
INSERT INTO Route VALUES
(1, 'Pune', 'Mumbai', 10),
(2, 'Pune', 'Nashik', 8);

-- Driver
INSERT INTO Driver VALUES
(101, 'Amit', 'LIC001', 'Addr1', 45, 35000),
(102, 'Riya', 'LIC002', 'Addr2', 30, 28000),
(103, 'Ajay', 'LIC003', 'Addr3', 50, 40000),
(104, 'Neha', 'LIC004', 'Addr4', 38, 32000);

-- Relationship
INSERT INTO Bus_Route_Driver VALUES
(10, 1, 101, 1, '2024-01-01'),
(11, 2, 102, 2, '2024-01-02'),
(12, 1, 103, 1, '2024-01-03'),
(12, 2, 104, 2, '2024-01-04');
```

1. Drivers having age more than 40
```sql
SELECT * FROM Driver
WHERE age > 40;
```

2. Driver name having salary > 30000
```sql
SELECT driver_name
FROM Driver
WHERE salary > 30000;
```

3. Insert 2 records into Route table
```sql
INSERT INTO Route VALUES
(3, 'Mumbai', 'Goa', 12),
(4, 'Pune', 'Kolhapur', 9);
```

4. Driver name who drives bus no 12
```sql
SELECT d.driver_name
FROM Driver d
JOIN Bus_Route_Driver brd ON d.Driver_no = brd.Driver_no
WHERE brd.Bus_no = 12;
```

1. Trigger before insert on Driver

Condition: age between 18 and 25 → error
```sql
CREATE OR REPLACE FUNCTION check_driver_age()
RETURNS TRIGGER AS $$
BEGIN
    IF NEW.age BETWEEN 18 AND 25 THEN
        RAISE EXCEPTION 'Invalid input';
    END IF;
    RETURN NEW;
END;
$$ LANGUAGE plpgsql;

CREATE TRIGGER trg_driver_age
BEFORE INSERT ON Driver
FOR EACH ROW
EXECUTE FUNCTION check_driver_age();
```

2. Stored Function: Bus details by Route_no
```sql
CREATE OR REPLACE FUNCTION get_bus_details(r_no INTEGER)
RETURNS TABLE (
    bus_no INTEGER,
    capacity INTEGER,
    depot_name VARCHAR
) AS $$
BEGIN
    RETURN QUERY
    SELECT b.Bus_no, b.capacity, b.depot_name
    FROM Bus b
    JOIN Bus_Route_Driver brd ON b.Bus_no = brd.Bus_no
    WHERE brd.Route_no = r_no;
END;
$$ LANGUAGE plpgsql;
```

Call Function
```sql
SELECT * FROM get_bus_details(1);
```
