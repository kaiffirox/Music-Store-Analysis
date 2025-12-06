# Music-Store-Analysis

# 🎵 Music Store Analysis — SQL Project  
### Author: **Mohammad Kaif Firoz**

A complete SQL-based data analysis project built using the **Music Store Database**.  
This project answers real-world business questions using advanced SQL techniques such as JOINs, CTEs, Window Functions, Subqueries, and Aggregations.

---

## 📌 Project Overview

This project explores a digital music store database to uncover insights related to:

- Customer spending behavior  
- Best-performing cities and countries  
- Most popular genres  
- Top artists by revenue and track count  
- Country-wise customer expenditure  
- High-value customers  

The project is divided into **3 difficulty levels**:

✔ Easy Queries  
✔ Moderate Queries  
✔ Advanced Analytical Queries  

---

## 🗂️ Database Tables Used

- `employee`
- `customer`
- `invoice`
- `invoice_line`
- `track`
- `album`
- `genre`
- `artist`

These tables simulate a real-world music store ecosystem.

---

## 🧰 Tech Stack

- **SQL (MySQL)**
- Joins  
- CTE (Common Table Expressions)  
- Window Functions  
- Subqueries  
- Aggregations  
- Data Cleaning Logic  

---

# 🧩 SQL QUERIES & SOLUTIONS

---

# 🟦 QUESTION SET 1 — EASY LEVEL

---

### ✅ Q1: Senior-most employee based on job title
```sql
SELECT title, last_name, first_name 
FROM employee
ORDER BY levels DESC
LIMIT 1;

---

✅ Q2: Countries with the most invoices
sql
SELECT COUNT(*) AS c, billing_country 
FROM invoice
GROUP BY billing_country
ORDER BY c DESC;



✅ Q3: Top 3 invoice totals
sql
SELECT * 
FROM invoice
ORDER BY total DESC
limit 3;



✅ Q4: City generating highest revenue
sql
SELECT billing_city, SUM(total) AS InvoiceTotal
FROM invoice
GROUP BY billing_city
ORDER BY InvoiceTotal DESC
LIMIT 1;



✅ Q5: Best customer (highest spending)
sql
Copy code
SELECT c.customer_id, concat(c.first_name, ' ',  c.last_name), SUM(i.total) AS total_spending
FROM customer c
JOIN invoice i ON c.customer_id = i.customer_id
GROUP BY c.customer_id, c.first_name, c.last_name
ORDER BY total_spending DESC
LIMIT 1;



 🟩 QUESTION SET 2 — MODERATE LEVEL

✅ Q1: Rock music listeners (email, names, genre)
Method 1
sql
Copy code
SELECT DISTINCT c.email, c.first_name, c.last_name
FROM customer c
JOIN invoice i ON c.customer_id = i.customer_id
JOIN invoice_line il ON i.invoice_id = il.invoice_id
WHERE track_id IN(
	SELECT t.track_id FROM track t
	JOIN genre g ON t.genre_id = g.genre_id
	WHERE g.name LIKE 'Rock'
)
ORDER BY email;
                                   Method 2

sql
SELECT DISTINCT email AS Email, first_name AS FirstName, last_name AS LastName, genre.name AS Name
FROM customer
JOIN invoice ON invoice.customer_id = customer.customer_id
JOIN invoice_line ON invoice_line.invoice_id = invoice.invoice_id
JOIN track ON track.track_id = invoice_line.track_id
JOIN genre ON genre.genre_id = track.genre_id
WHERE genre.name LIKE 'Rock'
ORDER BY email;




✅ Q2: Top 10 artists with most Rock tracks
sql
SELECT artist.artist_id, artist.name,COUNT(artist.artist_id) AS number_of_songs
FROM track
JOIN album2 ON album2.album_id = track.album_id
JOIN artist ON artist.artist_id = album2.artist_id
JOIN genre ON genre.genre_id = track.genre_id
WHERE genre.name LIKE 'Rock'
GROUP BY artist.artist_id, artist.name
ORDER BY number_of_songs DESC
LIMIT 10;




✅ Q3: Tracks longer than average length
sql
SELECT name, milliseconds
FROM track
WHERE milliseconds > (
	SELECT AVG(milliseconds) FROM track )
ORDER BY milliseconds DESC;




🟥 QUESTION SET 3 — ADVANCED LEVEL


✅ Q1: Amount spent by each customer on the best-selling artist
sql
WITH best_selling_artist AS (
    SELECT ar.artist_id, ar.name AS artist_name, 
           SUM(il.unit_price * il.quantity) AS total_sales
    FROM invoice_line il
    JOIN track t ON t.track_id = il.track_id
    JOIN album a ON a.album_id = t.album_id
    JOIN artist ar ON ar.artist_id = a.artist_id
    GROUP BY ar.artist_id, ar.name
    ORDER BY total_sales DESC
    LIMIT 1
)
SELECT 
    c.customer_id, 
    c.first_name, 
    c.last_name, 
    bsa.artist_name, 
    SUM(il.unit_price * il.quantity) AS amount_spent
FROM customer c
JOIN invoice i       ON i.customer_id = c.customer_id
JOIN invoice_line il ON il.invoice_id = i.invoice_id
JOIN track t         ON t.track_id = il.track_id
JOIN album alb       ON alb.album_id = t.album_id
JOIN best_selling_artist bsa ON bsa.artist_id = alb.artist_id
GROUP BY c.customer_id, c.first_name, c.last_name, bsa.artist_name
ORDER BY amount_spent DESC;




✅ Q2: Most popular genre in each country
sql
WITH popular_genre AS 
(
    SELECT COUNT(invoice_line.quantity) AS purchases, customer.country, genre.name, genre.genre_id, 
	ROW_NUMBER() OVER(PARTITION BY customer.country ORDER BY COUNT(invoice_line.quantity) DESC) AS RowNo 
    FROM invoice_line 
	JOIN invoice ON invoice.invoice_id = invoice_line.invoice_id
	JOIN customer ON customer.customer_id = invoice.customer_id
	JOIN track ON track.track_id = invoice_line.track_id
	JOIN genre ON genre.genre_id = track.genre_id
	GROUP BY 2,3,4
)
SELECT * FROM popular_genre WHERE RowNo = 1;



✅ Q3: Top-spending customer per country
sql
WITH Customer_with_country AS (
		SELECT customer.customer_id, first_name, last_name, billing_country, SUM(total) AS total_spending,
	    ROW_NUMBER() OVER(PARTITION BY billing_country ORDER BY SUM(total) DESC) AS RowNo 
		FROM invoice
		JOIN customer ON customer.customer_id = invoice.customer_id
		GROUP BY 1,2,3,4
)
SELECT * FROM Customer_with_country WHERE RowNo = 1;



🧠 Key Insights From the Project
USA & Canada generate the highest number of invoices

Rock is the most popular music genre worldwide

A few artists contribute most to store revenue

Customer spending differs significantly across countries

Some cities show exceptional revenue concentration

🎯 Business Value Delivered
✔ Helps identify high-value customers
✔ Highlights top-performing music genres
✔ Provides geographic spending behavior
✔ Useful for targeted marketing campaigns
✔ Helps understand artist-based revenue contribution

🚀 How to Run This Project
Create the Music Store database:

sql
CREATE DATABASE MUSIC_STORE;
USE MUSIC_STORE;
Import all tables

Run each query block

Analyze outputs

👨‍💻 Author
Mohammad Kaif Firoz
Data Analyst | SQL • Excel • Tableau • Power BI • Python

📧 Email: kaifsidd2003@gmail.com
🔗 LinkedIn: https://www.linkedin.com/in/kaiffiroz/

