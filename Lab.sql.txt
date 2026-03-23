-- ==========================================
-- EXERCISE 1: BLOG DATABASE (Normalization)
-- ==========================================


-- 1. Create Tables(DDL)--

CREATE TABLE AUTHORS(
    author_id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(100) NOT NULL
);

CREATE TABLE INFOS(
   info_id INT AUTO_INCREMENT PRIMARY KEY,
   title VARCHAR(100) NOT NULL,
   word_count INT NOT NULL,
   views INT NOT NULL,
   author_id INT,
   FOREIGN KEY(author_id) REFERENCES authors(author_id)
 );


--2. Insert Sample Data(DML)--

INSERT INTO AUTHORS (name) 
VALUES ("Maria Charlotte"), ("Juan Perez"), ("Gemma Alcocer");

INSERT INTO INFOS (author_id, title, word_count, views) 
VALUES
(1, "Best Paint Colors", 814, 14),             
(2, "Small Space Decorating Tips", 1146, 221),  
(1, "Hot Accessories", 986, 105),             
(1, "Mixing Textures", 765, 22),               
(2, "Kitchen Refresh", 1242, 307),  
(1, "Homemade Art Hacks", 1002, 193),          
(3, "Refinishing Wood Floors", 1571, 7542);

--3. RESULT--

SELECT a.name AS "author", i.title AS "title", i.word_count AS "word_count", i.views AS "views"
FROM authors a LEFT JOIN infos i ON a.author_id=i.author_id;








-- ======================================================
-- EXERCISE 2: AIRLINE DATABASE (3NF Normalization)
-- ======================================================

-- 1. Create Tables (DDL)

CREATE TABLE customers(
   customer_id INT AUTO_INCREMENT PRIMARY KEY,
   customer_name VARCHAR(100) NOT NULL,
   customer_status VARCHAR(50),
   total_customer_mileage INT
);

CREATE TABLE flights(
   flight_id INT AUTO_INCREMENT PRIMARY KEY,
   flight_number VARCHAR(5) NOT NULL,
   flight_mileage INT NOT NULL,
   aircraft_id INT,
   FOREIGN KEY(aircraft_id) REFERENCES      aircrafts(aircraft_id)
);


CREATE TABLE aircrafts(
   aircraft_id INT AUTO_INCREMENT PRIMARY KEY,
   aircraft VARCHAR(100) NOT NULL,
    total_seats INT NOT NULL CHECK (total_seats > 0)
);

CREATE TABLE bookings(
   booking_id INT AUTO_INCREMENT PRIMARY KEY,
   customer_id INT,
   flight_id INT,
   FOREIGN KEY(customer_id) REFERENCES customers(customer_id),
   FOREIGN KEY(flight_id) REFERENCES flights(flight_id)
) ;



-- 2. Insert Sample Data (DML)--

INSERT INTO aircrafts (aircraft, total_seats) VALUES 
('Boeing 747', 400),
('Airbus A330', 236),
('Boeing 777', 264);

INSERT INTO customers (customer_name, customer_status, total_customer_mileage) VALUES 
('Agustine Riviera', 'Silver', 115235),
('Alaina Sepulvida', 'None', 6008),
('Tom Jones', 'Gold', 205767),
('Sam Rio', 'None', 2653),
('Jessica James', 'Silver', 127656),
('Ana Janco', 'Silver', 136773),
('Jennifer Cortez', 'Gold', 300582),
('Christian Janco', 'Silver', 14642);

INSERT INTO flights (flight_number, flight_mileage, aircraft_id) VALUES 
('DL143', 135, 1),
('DL122', 4370, 2),
('DL53', 2078, 3),
('DL222', 1765, 3),
('DL37', 531, 1);

INSERT INTO bookings (customer_id, flight_id) VALUES 
(1, 1), (1, 2), (2, 2), (1, 1), (3, 2), 
(3, 3), (1, 1), (4, 1), (1, 1), (3, 4), 
(5, 1), (4, 1), (6, 4), (7, 4), (5, 2), 
(4, 5), (8, 4);




-- 3. RESULT--

SELECT c.customer_name AS "Customer Name",c.customer_status AS "Customer Status",
f.flight_number AS "Flight Number",a.aircraft AS "Aircraft",
a.total_seats AS "Total Aircraft Seats", f.flight_mileage AS "Flight Mileage",c.total_customer_mileage AS "Total Customer Mileage"
FROM customers c LEFT JOIN bookings b ON c.customer_id=b.customer_id 
LEFT JOIN flights f ON b.flight_id=f.flight_id 
LEFT JOIN aircrafts a ON f.aircraft_id=a.aircraft_id;






-- ======================================================
-- EXERCISE 3: AIRLINE DATABASE QUERIES
-- ======================================================

-- 1. Total number of flights--
SELECT COUNT(DISTINCT flight_number) FROM flights;


--2. Average flight distance--
SELECT AVG(flight_mileage) FROM flights;


--3. Average number of seats per aircraft--
SELECT AVG(total_seats) FROM aircrafts;


--4. Average miles flown by customers, grouped by status--
SELECT customer_status, AVG(total_customer_mileage) FROM customers GROUP BY customer_status;


--5.Max miles flown by customers, grouped by status--
SELECT customer_status, MAX(total_customer_mileage) FROM customers GROUP BY customer_status;


--6. Number of aircrafts with "Boeing" in their name--
SELECT COUNT(*) FROM aircrafts WHERE aircraft LIKE '%Boeing%';


--7. Flights with distance between 300 and 2000 miles--
SELECT * FROM flights WHERE flight_mileage BETWEEN 300 AND 2000;


--8. Average flight distance booked, grouped by customer status--
SELECT c.customer_status, AVG(f.flight_mileage)
FROM bookings b
JOIN customers c ON b.customer_id = c.customer_id
JOIN flights f ON b.flight_id = f.flight_id
GROUP BY c.customer_status;


--9.Most booked aircraft among Gold status members--
SELECT a.aircraft, COUNT(*) AS total_bookings
FROM bookings b
JOIN customers c ON b.customer_id = c.customer_id
JOIN flights f ON b.flight_id = f.flight_id
JOIN aircrafts a ON f.aircraft_id = a.aircraft_id
WHERE c.customer_status = 'Gold'
GROUP BY a.aircraft
ORDER BY total_bookings DESC
LIMIT 1;



