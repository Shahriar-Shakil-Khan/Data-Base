DROP DATABASE "Vehicle_Rental_System";

-- For user Table and insertion
CREATE TABLE IF NOT EXISTS users (
    user_id SERIAL PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    email VARCHAR(150) UNIQUE NOT NULL CHECK (email = LOWER(email)),
    password VARCHAR(255) NOT NULL,
    phone VARCHAR(15) NOT NULL,
    role VARCHAR(20) NOT NULL CHECK (role IN ('admin', 'customer'))
);

INSERT INTO users (name, email, password, phone, role) VALUES
('Karim Ahmed', 'karim@gmail.com', 'pass2345', '01722222222', 'customer'),
('Abdul Kader', 'kader@gmail.com', 'pass3456', '01733333333', 'admin'),
('Nusrat Jahan', 'nusrat@gmail.com', 'pass4567', '01744444444', 'customer')

drop table users

-- For user vehicles and insertion 
CREATE TABLE IF NOT EXISTS vehicles(
    vehicle_id SERIAL PRIMARY KEY,
    name VARCHAR(150) NOT NULL,
    type VARCHAR(20) NOT NULL CHECK (type IN ('car', 'bike', 'truck')),
    model VARCHAR(15) NOT NULL,
    registration_number VARCHAR(100) UNIQUE NOT NULL,
    rental_price NUMERIC(10,2) NOT NULL CHECK (rental_price > 0),
    status VARCHAR(20) CHECK (status IN ('available', 'rented', 'maintenance'))
);

INSERT INTO vehicles (name, type, model, registration_number, rental_price, status) VALUES 
('Toyota Corolla', 'car',   2022, 'ABC-123', 50,  'available'),
('Honda Civic',    'car',   2021, 'DEF-456', 60,  'rented'),
('Yamaha R15',     'bike',  2023, 'GHI-789', 30,  'available'),
('Ford F-150',     'truck', 2020, 'JKL-012', 100, 'maintenance');

drop table vehicles
  
-- For user bookings and insertion

CREATE TABLE IF NOT EXISTS bookings(
    booking_id SERIAL PRIMARY KEY,
    user_id INT NOT NULL REFERENCES users(user_id) ON DELETE CASCADE,
    vehicle_id INT NOT NULL REFERENCES vehicles(vehicle_id) ON DELETE CASCADE,
    start_date DATE NOT NULL,
    end_date DATE NOT NULL,
    status VARCHAR(20) NOT NULL DEFAULT 'pending' CHECK (status IN ('pending', 'confirmed', 'completed', 'cancelled')),
    total_cost NUMERIC(10,2) NOT NULL,
    CHECK (end_date >= start_date)
);

INSERT INTO bookings (user_id, vehicle_id, start_date, end_date, status, total_cost)
VALUES
(1, 2, '2023-10-01', '2023-10-05', 'completed', 240),
(1, 2, '2023-11-01', '2023-11-03', 'completed', 120),
(3, 2, '2023-12-01', '2023-12-02', 'confirmed',  60),
(1, 1, '2023-12-10', '2023-12-12', 'pending',   100);


INSERT INTO vehicles (name, type, model, registration_number, rental_price, status) VALUES
('Toyota Hiace', 'car', '2023', 'DHAKA-1005', 6000.00, 'available'),
('Honda Beat', 'bike', '2022', 'DHAKA-2004', 1500.00, 'available'),
('Ashok Leyland', 'truck', '2021', 'DHAKA-3004', 12000.00, 'available'),
('Suzuki Gixxer', 'bike', '2023', 'DHAKA-2005', 2200.00, 'available'),
('Mitsubishi Canter', 'truck', '2020', 'DHAKA-3005', 9000.00, 'available');


-- Query 1: JOIN(INNER JOIN)

SELECT 
    bookings.booking_id,
    users.name AS customer_name,
    vehicles.name AS vehicle_name,
    bookings.start_date,
    bookings.end_date,
    bookings.status
FROM bookings
INNER JOIN users ON bookings.user_id = users.user_id
INNER JOIN vehicles ON bookings.vehicle_id = vehicles.vehicle_id;

-- Query 2: EXISTS

SELECT
    v.vehicle_id,
    v.name,
    v.type,
    v.model,
    v.registration_number,
    v.rental_price,
    v.status
FROM vehicles v
WHERE NOT EXISTS (
    SELECT 1
    FROM bookings b
    WHERE b.vehicle_id = v.vehicle_id
);

-- Query 3: WHERE
SELECT *
FROM vehicles
WHERE status = 'available'
  AND type = 'car';

-- Query 4: GROUP BY and HAVING

SELECT
    v.vehicle_id,
    v.name AS vehicle_name,
    COUNT(b.booking_id) AS total_bookings
FROM vehicles v
INNER JOIN bookings b ON v.vehicle_id = b.vehicle_id
GROUP BY v.vehicle_id, v.name
HAVING COUNT(b.booking_id) > 2;


Q1: What is a foreign key?
A foreign key is a column in one table that points to the primary key of another table. It is used to link two tables together and maintain data consistency. For example, an orders table may have a customer_id column that references the customers table.

Q2: WHERE vs HAVING
WHERE filters rows before grouping, while HAVING filters after grouping. WHERE cannot use aggregate functions like COUNT or SUM, but HAVING can. For example, use WHERE to filter individual records and HAVING to filter grouped results.

Q3: What is a primary key?
A primary key is a column that uniquely identifies each row in a table. It cannot be NULL and must be unique. Every table should have one primary key. For example, student_id in a students table.

Q4: INNER JOIN vs LEFT JOIN
INNER JOIN returns only the rows that have matching values in both tables. LEFT JOIN returns all rows from the left table, and the matching rows from the right table. If there is no match, it returns NULL for the right table columns.





-------------------------------------
# Sample SQL Queries - Expected Output

This document provides sample data and the expected outputs for the SQL queries defined in the assignment.

## Sample Data (Input)

### Users Table
| user_id | name | email | phone | role |
| :--- | :--- | :--- | :--- | :--- |
| 1 | Alice | alice@example.com | 1234567890 | Customer |
| 2 | Bob | bob@example.com | 0987654321 | Admin |
| 3 | Charlie | charlie@example.com | 1122334455 | Customer |

### Vehicles Table
| vehicle_id | name | type | model | registration_number | rental_price | status |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| 1 | Toyota Corolla | car | 2022 | ABC-123 | 50 | available |
| 2 | Honda Civic | car | 2021 | DEF-456 | 60 | rented |
| 3 | Yamaha R15 | bike | 2023 | GHI-789 | 30 | available |
| 4 | Ford F-150 | truck | 2020 | JKL-012 | 100 | maintenance |

### Bookings Table
| booking_id | user_id | vehicle_id | start_date | end_date | status | total_cost |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| 1 | 1 | 2 | 2023-10-01 | 2023-10-05 | completed | 240 |
| 2 | 1 | 2 | 2023-11-01 | 2023-11-03 | completed | 120 |
| 3 | 3 | 2 | 2023-12-01 | 2023-12-02 | confirmed | 60 |
| 4 | 1 | 1 | 2023-12-10 | 2023-12-12 | pending | 100 |

---

## Expected Query Results

### Query 1: JOIN
**Requirement**: Retrieve booking information along with Customer name and Vehicle name.

**Expected Output**:
| booking_id | customer_name | vehicle_name | start_date | end_date | status |
| :--- | :--- | :--- | :--- | :--- | :--- |
| 1 | Alice | Honda Civic | 2023-10-01 | 2023-10-05 | completed |
| 2 | Alice | Honda Civic | 2023-11-01 | 2023-11-03 | completed |
| 3 | Charlie | Honda Civic | 2023-12-01 | 2023-12-02 | confirmed |
| 4 | Alice | Toyota Corolla | 2023-12-10 | 2023-12-12 | pending |

---

### Query 2: EXISTS
**Requirement**: Find all vehicles that have never been booked.

**Expected Output**:
| vehicle_id | name | type | model | registration_number | rental_price | status |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| 3 | Yamaha R15 | bike | 2023 | GHI-789 | 30 | available |
| 4 | Ford F-150 | truck | 2020 | JKL-012 | 100 | maintenance |

---

### Query 3: WHERE
**Requirement**: Retrieve all available vehicles of a specific type (e.g. cars).

**Expected Output**:
| vehicle_id | name | type | model | registration_number | rental_price | status |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| 1 | Toyota Corolla | car | 2022 | ABC-123 | 50 | available |

---

### Query 4: GROUP BY and HAVING
**Requirement**: Find the total number of bookings for each vehicle and display only those vehicles that have more than 2 bookings.

**Expected Output**:
| vehicle_name | total_bookings |
| :--- | :--- |
| Honda Civic | 3 |