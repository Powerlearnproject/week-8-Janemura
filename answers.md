CREATE DATABASE land_transactions;
USE land_transactions;
CREATE TABLE Users (
    user_id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    email VARCHAR(100) UNIQUE NOT NULL,
    phone_number VARCHAR(20) NOT NULL,
    user_type ENUM('buyer', 'seller', 'legal_assistant') NOT NULL,
    verified_status BOOLEAN DEFAULT FALSE
);

CREATE TABLE Land_Listings (
    listing_id INT AUTO_INCREMENT PRIMARY KEY,
    user_id INT NOT NULL,
    location VARCHAR(255) NOT NULL,
    size DECIMAL(10,2) NOT NULL,  -- Size in square meters
    price DECIMAL(12,2) NOT NULL, -- Price in currency
    status ENUM('available', 'sold', 'disputed') DEFAULT 'available',
    FOREIGN KEY (user_id) REFERENCES Users(user_id) ON DELETE CASCADE
);

CREATE TABLE Transactions (
    transaction_id INT AUTO_INCREMENT PRIMARY KEY,
    buyer_id INT NOT NULL,
    seller_id INT NOT NULL,
    listing_id INT NOT NULL,
    transaction_date DATE NOT NULL,
    amount DECIMAL(12,2) NOT NULL,
    status ENUM('pending', 'completed', 'disputed') DEFAULT 'pending',
    FOREIGN KEY (buyer_id) REFERENCES Users(user_id) ON DELETE CASCADE,
    FOREIGN KEY (seller_id) REFERENCES Users(user_id) ON DELETE CASCADE,
    FOREIGN KEY (listing_id) REFERENCES Land_Listings(listing_id) ON DELETE CASCADE
);

CREATE TABLE Land_Disputes (
    dispute_id INT AUTO_INCREMENT PRIMARY KEY,
    listing_id INT NOT NULL,
    user_id INT NOT NULL,
    issue_description TEXT NOT NULL,
    resolution_status ENUM('pending', 'resolved') DEFAULT 'pending',
    FOREIGN KEY (listing_id) REFERENCES Land_Listings(listing_id) ON DELETE CASCADE,
    FOREIGN KEY (user_id) REFERENCES Users(user_id) ON DELETE CASCADE
);

CREATE TABLE Legal_Assistance (
    legal_id INT AUTO_INCREMENT PRIMARY KEY,
    user_id INT NOT NULL,
    listing_id INT NOT NULL,
    assistance_type VARCHAR(255) NOT NULL,
    status ENUM('active', 'closed') DEFAULT 'active',
    FOREIGN KEY (user_id) REFERENCES Users(user_id) ON DELETE CASCADE,
    FOREIGN KEY (listing_id) REFERENCES Land_Listings(listing_id) ON DELETE CASCADE
);

INSERT INTO Users (name, email, phone_number, user_type, verified_status)
VALUES 
('Mwangi Kamau', 'mwangi.kamau@example.com', '0712345678', 'buyer', TRUE),
('Akinyi Otieno', 'akinyi.otieno@example.com', '0722345678', 'seller', FALSE),  -- Unverified
('Mutua Njoroge', 'mutua.njoroge@example.com', '0733345678', 'legal_assistant', TRUE),
('Wanjiru Karanja', 'wanjiru.karanja@example.com', '0744445678', 'buyer', FALSE),  
('Omondi Ochieng', 'omondi.ochieng@example.com', '0755555678', 'seller', TRUE),
('Nafula Wekesa', 'nafula.wekesa@example.com', '0766665678', 'legal_assistant', FALSE),  
('Cherono Kipkorir', 'cherono.kipkorir@example.com', '0777775678', 'buyer', TRUE),
('Abdi Mohamed', 'abdi.mohamed@example.com', '0788885678', 'seller', TRUE),
('Fatuma Hassan', 'fatuma.hassan@example.com', '0799995678', 'legal_assistant', FALSE),  
('Juma Makau', 'juma.makau@example.com', '0711223344', 'buyer', TRUE),
('Moraa Nyaboke', 'moraa.nyaboke@example.com', '0722334455', 'seller', TRUE),
('Otieno Oketch', 'otieno.oketch@example.com', '0733445566', 'legal_assistant', TRUE);

INSERT INTO Land_Listings (user_id, location, size, price, status)
VALUES 
(2, 'Kilimani, Nairobi', 500.00, 5000000, 'available'),
(2, 'Nyali, Mombasa', 300.00, 3500000, 'sold'),
(5, 'Eldoret, Uasin Gishu', 700.00, 4500000, 'available'),
(5, 'Kisumu Central, Kisumu', 600.00, 4000000, 'available'),
(2, 'Karen, Nairobi', 1000.00, 15000000, 'sold'),
(8, 'Westlands, Nairobi', 550.00, 8000000, 'available'),
(8, 'Malindi, Kilifi', 450.00, 5000000, 'available'),
(11, 'Nakuru Town, Nakuru', 350.00, 3000000, 'sold'),
(11, 'Kakamega Town, Kakamega', 620.00, 4200000, 'available'),
(5, 'Machakos Town, Machakos', 900.00, 7500000, 'available'),
(2, 'Ruiru, Kiambu', 800.00, 6800000, 'available'),
(8, 'Kitengela, Kajiado', 400.00, 6000000, 'sold');

INSERT INTO Transactions (buyer_id, seller_id, listing_id, transaction_date, amount, status)
VALUES 
(1, 2, 1, '2024-01-10', 5000000, 'completed'),
(4, 5, 3, '2024-02-05', 4500000, 'pending'),
(1, 2, 5, '2024-01-20', 15000000, 'completed'),
(7, 8, 6, '2024-02-15', 8000000, 'completed'),
(4, 11, 8, '2024-03-01', 3000000, 'pending'),
(10, 5, 10, '2024-03-10', 7500000, 'completed'),
(7, 2, 11, '2024-03-12', 6800000, 'pending'),
(1, 8, 7, '2024-03-15', 5000000, 'completed'),
(10, 11, 9, '2024-03-18', 4200000, 'pending'),
(7, 8, 12, '2024-03-20', 6000000, 'completed'),
(4, 5, 4, '2024-03-25', 4000000, 'completed'),
(1, 2, 2, '2024-04-01', 3500000, 'completed');

INSERT INTO Land_Disputes (listing_id, user_id, issue_description, resolution_status)
VALUES 
(1, 1, 'Boundary dispute with neighboring property in Kilimani.', 'pending'),
(3, 4, 'Seller did not provide full documentation for Eldoret property.', 'resolved'),
(5, 1, 'Claims of double ownership for Karen land.', 'pending'),
(8, 4, 'Incorrect land size registered in Nakuru Town.', 'resolved'),
(10, 10, 'Land ownership transfer delayed for Machakos property.', 'pending'),
(12, 7, 'Seller changed selling price after agreement in Kitengela.', 'pending'),
(2, 1, 'Fraudulent listing in Nyali, buyer misled.', 'resolved'),
(6, 7, 'Westlands land had unpaid property tax issues.', 'pending'),
(9, 10, 'Multiple ownership claims on Kakamega land.', 'pending'),
(4, 4, 'Confusion over zoning restrictions in Kisumu.', 'resolved'),
(7, 1, 'Seller provided forged documents for Malindi.', 'pending'),
(11, 7, 'Incomplete documentation for Ruiru property.', 'resolved');

INSERT INTO Legal_Assistance (user_id, listing_id, assistance_type, status)
VALUES 
(3, 1, 'Verification of land ownership documents.', 'active'),
(6, 3, 'Mediation for ownership dispute in Eldoret.', 'closed'),
(3, 5, 'Fraud investigation for Karen property.', 'active'),
(9, 8, 'Title deed verification for Nakuru land.', 'active'),
(12, 10, 'Legal representation in Kakamega land case.', 'closed'),
(3, 12, 'Property boundary review for Kitengela.', 'active'),
(6, 6, 'Dispute resolution for Westlands property.', 'active'),
(9, 9, 'Land inheritance case in Kakamega.', 'closed'),
(3, 4, 'Government land verification in Kisumu.', 'active'),
(6, 7, 'Assisting buyer in Malindi property dispute.', 'closed'),
(9, 11, 'Title transfer process review in Ruiru.', 'active'),
(3, 2, 'Legal consultation on property laws.', 'closed');

SELECT * FROM Users;
SELECT * FROM Land_Listings;
SELECT * FROM Transactions;
SELECT * FROM Land_Disputes;
SELECT * FROM Legal_Assistance;

SELECT * 
FROM Users
WHERE verified_status = FALSE;

SELECT listing_id, location, size, price, status
FROM Land_Listings
WHERE status = 'available';

SELECT t.transaction_id, b.name AS buyer, s.name AS seller, l.location, t.transaction_date, t.amount, t.status
FROM Transactions t
JOIN Users b ON t.buyer_id = b.user_id
JOIN Users s ON t.seller_id = s.user_id
JOIN Land_Listings l ON t.listing_id = l.listing_id
WHERE t.status = 'completed';

SELECT t.transaction_id, b.name AS buyer, s.name AS seller, l.location, t.transaction_date, t.amount, t.status
FROM Transactions t
JOIN Users b ON t.buyer_id = b.user_id
JOIN Users s ON t.seller_id = s.user_id
JOIN Land_Listings l ON t.listing_id = l.listing_id
WHERE t.status = 'pending';

SELECT d.dispute_id, l.location, d.issue_description, d.resolution_status, u.name AS user_name
FROM Land_Disputes d
JOIN Land_Listings l ON d.listing_id = l.listing_id
JOIN Users u ON d.user_id = u.user_id
WHERE d.resolution_status = 'pending';

SELECT la.legal_id AS assistance_id, u.name AS user_name, l.location, la.assistance_type, la.status
FROM Legal_Assistance la
JOIN Users u ON la.user_id = u.user_id
JOIN Land_Listings l ON la.listing_id = l.listing_id
WHERE la.status = 'active';

SELECT l.location, l.size, l.price, d.issue_description, d.resolution_status
FROM Land_Listings l
JOIN Land_Disputes d ON l.listing_id = d.listing_id
WHERE d.resolution_status = 'pending';

SELECT location, size, price, status
FROM Land_Listings
WHERE user_id = (SELECT user_id FROM Users WHERE name = 'Mwangi Kamau');

SELECT DISTINCT b.name AS buyer, t.transaction_date, l.location, t.amount
FROM Transactions t
JOIN Users b ON t.buyer_id = b.user_id
JOIN Land_Listings l ON t.listing_id = l.listing_id
WHERE t.status = 'completed';

SELECT SUM(amount) AS total_amount
FROM Transactions
WHERE status = 'completed';

SELECT u.name AS user_name, la.assistance_type, la.status
FROM Legal_Assistance la
JOIN Users u ON la.user_id = u.user_id
ORDER BY u.name;

SELECT l.location, COUNT(d.dispute_id) AS unresolved_disputes
FROM Land_Disputes d
JOIN Land_Listings l ON d.listing_id = l.listing_id
WHERE d.resolution_status = 'pending'
GROUP BY l.location;

SELECT status, COUNT(*) AS total_listings
FROM Land_Listings
GROUP BY status;

SELECT SUM(amount) AS total_transaction_amount
FROM Transactions
WHERE status = 'completed';

SELECT verified_status, COUNT(*) AS total_users
FROM Users
GROUP BY verified_status;

SELECT l.location, COUNT(d.dispute_id) AS total_disputes
FROM Land_Disputes d
JOIN Land_Listings l ON d.listing_id = l.listing_id
GROUP BY l.location
ORDER BY total_disputes DESC;

SELECT resolution_status, COUNT(*) AS total_disputes
FROM Land_Disputes
WHERE resolution_status = 'pending'
GROUP BY resolution_status;

SELECT AVG(DATEDIFF(t.transaction_date, t.transaction_date)) AS avg_transaction_time
FROM Transactions t
JOIN Land_Listings l ON t.listing_id = l.listing_id
WHERE t.status = 'completed';

SELECT assistance_type, COUNT(*) AS total_requests
FROM Legal_Assistance
GROUP BY assistance_type
ORDER BY total_requests DESC;

SELECT u.name AS user_name, COUNT(t.transaction_id) AS total_transactions
FROM Transactions t
JOIN Users u ON t.buyer_id = u.user_id
WHERE t.status = 'completed'
GROUP BY u.name
ORDER BY total_transactions DESC;

SELECT l.location, AVG(l.price) AS avg_price
FROM Land_Listings l
GROUP BY l.location
ORDER BY avg_price DESC;

SELECT la.status, COUNT(*) AS total_assistance_requests
FROM Legal_Assistance la
GROUP BY la.status
ORDER BY total_assistance_requests DESC;

SELECT u.verified_status, COUNT(t.transaction_id) AS total_transactions
FROM Transactions t
JOIN Users u ON t.buyer_id = u.user_id
WHERE t.status = 'completed'
GROUP BY u.verified_status;

SELECT 
    CASE
        WHEN d.dispute_id IS NOT NULL THEN 'With Dispute'
        ELSE 'Without Dispute'
    END AS dispute_status,
    COUNT(l.listing_id) AS total_listings
FROM Land_Listings l
LEFT JOIN Land_Disputes d ON l.listing_id = d.listing_id
GROUP BY dispute_status;

SELECT l.location, AVG(t.amount) AS avg_transaction_amount
FROM Transactions t
JOIN Land_Listings l ON t.listing_id = l.listing_id
WHERE t.status = 'completed'
GROUP BY l.location;

SELECT t.status, COUNT(*) AS total_transactions
FROM Transactions t
GROUP BY t.status;

SELECT DATE(la.request_date) AS request_date, COUNT(*) AS total_requests
FROM Legal_Assistance la
GROUP BY request_date
ORDER BY request_date DESC;


describe Legal_Assistance;
ALTER TABLE Legal_Assistance 
ADD COLUMN request_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP;
SELECT DATE(la.request_date) AS request_date, COUNT(*) AS total_requests
FROM Legal_Assistance la
GROUP BY request_date
ORDER BY request_date DESC;








































