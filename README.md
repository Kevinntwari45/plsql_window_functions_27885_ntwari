# plsql_window_functions_27885_ntwari

Course: Database Development with PL/SQL (INSY 8311)
Instructor: Eric Maniraguha
Student: NTWARI kevin
Student ID: 27885
Group: C
Submission Date: 02/06/2026

Business Problem
ABC Retail is an online e-commerce company operating in the electronics and home appliances sector. The company serves customers across multiple regions and manages a large catalog of products.
The Sales & Marketing Department requires advanced analytics to monitor performance and support strategic decision-making.

Data Challenge

The organization lacks consolidated insights into:

Regional product performance variations

Customer purchasing frequency and segmentation

Monthly and quarterly revenue trends

Identification of unsold or underperforming products

Customer retention and churn patterns

Raw transactional data exists but is not structured for analytical reporting.


DATABASE SCHAMA

1. customers

Stores customer demographic and contact information.

Column	Description
customer_id	Unique customer ID
customer_name	Full name
email	Contact email
region	Customer region
2. products

Stores product catalog details.

Column	Description
product_id	Unique product ID
product_name	Product name
category	Product category
price	Unit price
3. orders

Stores transactional sales records.

Column	Description
order_id	Unique order ID
customer_id	FK → customers
product_id	FK → products
order_date	Transaction date
quantity	Units sold
total_amount	Total order value

ER DIAGRAM
One customer → Many orders

One product → Many orders

Orders table links customers & products

customers ───< orders >─── products


JOIN QUERRY

Customer Orders With Product Details
SELECT
    c.customer_name,
    p.product_name,
    o.quantity,
    o.total_amount,
    o.order_date
FROM orders o
JOIN customers c
    ON o.customer_id = c.customer_id
JOIN products p
    ON o.product_id = p.product_id;
    
Revenue by Region    
SELECT
    c.region,
    SUM(o.total_amount) AS total_revenue
FROM orders o
JOIN customers c
    ON o.customer_id = c.customer_id
GROUP BY c.region
ORDER BY total_revenue DESC;


Products With No Sales
SELECT
    p.product_name
FROM products p
LEFT JOIN orders o
    ON p.product_id = o.product_id
WHERE o.product_id IS NULL;

Top Products per Region
SELECT *
FROM (
    SELECT
        c.region,
        p.product_name,
        SUM(o.total_amount) AS revenue,
        RANK() OVER (
            PARTITION BY c.region
            ORDER BY SUM(o.total_amount) DESC
        ) AS rank_position
    FROM orders o
    JOIN customers c
        ON o.customer_id = c.customer_id
    JOIN products p
        ON o.product_id = p.product_id
    GROUP BY c.region, p.product_name
)
WHERE rank_position <= 5;


Customer Purchase Ranking
SELECT
    c.customer_name,
    SUM(o.total_amount) AS total_spent,
    RANK() OVER (
        ORDER BY SUM(o.total_amount) DESC
    ) AS spending_rank
FROM orders o
JOIN customers c
    ON o.customer_id = c.customer_id
GROUP BY c.customer_name;


Monthly Sales Trend

SELECT
    TO_CHAR(order_date, 'YYYY-MM') AS month,
    SUM(total_amount) AS monthly_sales,
    SUM(SUM(total_amount)) OVER (
        ORDER BY TO_CHAR(order_date, 'YYYY-MM')
    ) AS running_total
FROM orders
GROUP BY TO_CHAR(order_date, 'YYYY-MM');

Product Sales Contribution %
SELECT
    product_name,
    revenue,
    ROUND(
        revenue * 100 /
        SUM(revenue) OVER (),
        2
    ) AS contribution_percent
FROM (
    SELECT
        p.product_name,
        SUM(o.total_amount) AS revenue
    FROM orders o
    JOIN products p
        ON o.product_id = p.product_id
    GROUP BY p.product_name
);


Key Insights

From the SQL analytics performed:

Certain products dominate sales within specific regions.

High-value customers contribute a large share of revenue.

Sales show clear monthly growth trends.

Some products have zero sales → inventory risk.

Revenue contribution is concentrated in a few categories.

References
https://www.w3schools.com/
Oracle PL/SQL Documentation
https://mode.com/sql-tutorial/sql-window-functions/
https://www.postgresql.org/docs/15/tutorial-window.html

Integrity Statement
I hereby declare that this project is my original academic work. All SQL scripts, analysis, and documentation were developed and executed by me as part of the Database Development with PL/SQL course.

Product portfolio decisions


    

    
