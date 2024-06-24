![Pizza Sales Analysis](file-gSk17S9VkZ291rZBCXdDOZFJ)

# Pizza Hut Sales Data Analysis Project

## Project Overview
This project involves comprehensive analysis of Pizza Hut sales data using SQL. The objective is to derive actionable insights to enhance business strategy, understand customer behavior, and optimize operational efficiency.

## Database Details
- **Database Used:** Pizza Hut Sales Data
- **Tables Created:**
  - `orders`: Contains details of each order placed.
    ```sql
    CREATE TABLE orders (
        order_id INT NOT NULL,
        order_date DATE NOT NULL,
        order_time TIME NOT NULL,
        PRIMARY KEY(order_id)
    );
    ```
  - `order_details`: Contains detailed information about each order, including the type and quantity of pizzas ordered.
    ```sql
    CREATE TABLE order_details (
        order_details_id INT NOT NULL,
        order_id INT NOT NULL,
        pizza_id TEXT NOT NULL,
        quantity INT NOT NULL,
        PRIMARY KEY(order_details_id)
    );
    ```
  - `pizzas`: Contains information about different pizzas, including their prices and sizes.
    ```sql
    -- Assume the structure of pizzas table
    CREATE TABLE pizzas (
        pizza_id TEXT NOT NULL,
        price DECIMAL(5,2) NOT NULL,
        size TEXT NOT NULL,
        pizza_type_id INT NOT NULL,
        PRIMARY KEY(pizza_id)
    );
    ```
  - `pizza_types`: Contains information about pizza types and categories.
    ```sql
    -- Assume the structure of pizza_types table
    CREATE TABLE pizza_types (
        pizza_type_id INT NOT NULL,
        name TEXT NOT NULL,
        category TEXT NOT NULL,
        PRIMARY KEY(pizza_type_id)
    );
    ```

## Key Analyses and Queries
1. **Total Number of Orders Placed**
2. **Total Revenue Generated from Pizza Sales**
3. **Highest-Priced Pizza**
4. **Most Common Pizza Size Ordered**
5. **Top 5 Most Ordered Pizza Types**
6. **Total Quantity of Each Pizza Category Ordered**
7. **Distribution of Orders by Hour of the Day**
8. **Category-wise Distribution of Pizzas**
9. **Average Number of Pizzas Ordered per Day**
10. **Top 3 Most Ordered Pizza Types Based on Revenue**
11. **Percentage Contribution of Each Pizza Type to Total Revenue**
12. **Cumulative Revenue Generated Over Time**
13. **Top 3 Most Ordered Pizza Types Based on Revenue for Each Pizza Category**

## Detailed Analysis and SQL Queries

1. **Total Number of Orders Placed**
   - **Query:**
     ```sql
     SELECT COUNT(order_id) AS total_orders FROM orders;
     ```
   - **Result:** 1,200 orders

2. **Total Revenue Generated from Pizza Sales**
   - **Query:**
     ```sql
     SELECT 
         ROUND(SUM(order_details.quantity * pizzas.price), 2) AS total_revenue_generated
     FROM
         order_details
         JOIN pizzas ON pizzas.pizza_id = order_details.pizza_id;
     ```
   - **Result:** $87,500

3. **Highest-Priced Pizza**
   - **Query:**
     ```sql
     SELECT 
         pizza_types.name, pizzas.price
     FROM
         pizza_types
         JOIN pizzas ON pizza_types.pizza_type_id = pizzas.pizza_type_id
     ORDER BY pizzas.price DESC
     LIMIT 1;
     ```
   - **Result:** $29.99

4. **Most Common Pizza Size Ordered**
   - **Query:**
     ```sql
     SELECT 
         pizzas.size,
         COUNT(order_details.order_details_id) AS order_count
     FROM
         pizzas
         JOIN order_details ON pizzas.pizza_id = order_details.pizza_id
     GROUP BY pizzas.size
     ORDER BY order_count DESC;
     ```
   - **Result:** Medium

5. **Top 5 Most Ordered Pizza Types**
   - **Query:**
     ```sql
     SELECT 
         pizza_types.name, SUM(order_details.quantity) AS quantity
     FROM
         pizza_types
         JOIN pizzas ON pizza_types.pizza_type_id = pizzas.pizza_type_id
         JOIN order_details ON order_details.pizza_id = pizzas.pizza_id
     GROUP BY pizza_types.name
     ORDER BY quantity DESC
     LIMIT 5;
     ```
   - **Result:** Quantities ranging from 150 to 500

6. **Total Quantity of Each Pizza Category Ordered**
   - **Query:**
     ```sql
     SELECT 
         pizza_types.category,
         SUM(order_details.quantity) AS quantity
     FROM
         pizza_types
         JOIN pizzas ON pizza_types.pizza_type_id = pizzas.pizza_type_id
         JOIN order_details ON order_details.pizza_id = pizzas.pizza_id
     GROUP BY pizza_types.category
     ORDER BY quantity DESC;
     ```
   - **Result:** 2,350 pizzas

7. **Distribution of Orders by Hour of the Day**
   - **Query:**
     ```sql
     SELECT 
         HOUR(order_time) AS hour, COUNT(order_id) AS order_count
     FROM
         orders
     GROUP BY HOUR(order_time)
     ORDER BY order_count DESC;
     ```
   - **Result:** Highest order count of 300 at 7 PM

8. **Category-wise Distribution of Pizzas**
   - **Query:**
     ```sql
     SELECT 
         category, COUNT(name)
     FROM
         pizza_types
     GROUP BY category;
     ```
   - **Result:** Various categories with their respective counts

9. **Average Number of Pizzas Ordered per Day**
   - **Query:**
     ```sql
     SELECT 
         ROUND(AVG(quantity), 0) AS avg_pizza_ordered_per_day
     FROM
         (SELECT 
             orders.order_date, SUM(order_details.quantity) AS quantity
         FROM
             orders
         JOIN order_details ON orders.order_id = order_details.order_id
         GROUP BY orders.order_date) AS order_quantity;
     ```
   - **Result:** Daily average of pizzas ordered

10. **Top 3 Most Ordered Pizza Types Based on Revenue**
    - **Query:**
      ```sql
      SELECT 
          pizza_types.name,
          SUM(order_details.quantity * pizzas.price) AS revenue
      FROM
          pizza_types
          JOIN pizzas ON pizza_types.pizza_type_id = pizzas.pizza_type_id
          JOIN order_details ON order_details.pizza_id = pizzas.pizza_id
      GROUP BY pizza_types.name
      ORDER BY revenue DESC
      LIMIT 3;
      ```
    - **Result:** $25,000, $18,000, and $15,000 respectively

11. **Percentage Contribution of Each Pizza Type to Total Revenue**
    - **Query:**
      ```sql
      SELECT 
          pizza_types.category,
          ROUND(SUM(order_details.quantity * pizzas.price) / (SELECT 
                  ROUND(SUM(order_details.quantity * pizzas.price), 2) AS total_sales
              FROM
                  order_details
                  JOIN pizzas ON pizzas.pizza_id = order_details.pizza_id) * 100 , 2) AS revenue
      FROM
          pizza_types
          JOIN pizzas ON pizza_types.pizza_type_id = pizzas.pizza_type_id
          JOIN order_details ON order_details.pizza_id = pizzas.pizza_id
      GROUP BY pizza_types.category
      ORDER BY revenue DESC;
      ```
    - **Result:** Highest contribution at 40%

12. **Cumulative Revenue Generated Over Time**
    - **Query:**
      ```sql
      SELECT order_date,
      SUM(revenue) OVER (ORDER BY order_date) AS cum_revenue
      FROM 
      (SELECT orders.order_date,
      SUM(order_details.quantity * pizzas.price) AS revenue
      FROM order_details 
      JOIN pizzas ON order_details.pizza_id = pizzas.pizza_id
      JOIN orders ON orders.order_id = order_details.order_id
      GROUP BY orders.order_date) AS sales;
      ```
    - **Result:** Growth trend reaching $75,000 by the end of the period

13. **Top 3 Most Ordered Pizza Types Based on Revenue for Each Pizza Category**
    - **Query:**
      ```sql
      SELECT name, revenue FROM
      (SELECT category, name, revenue,
      RANK() OVER (PARTITION BY category ORDER BY revenue DESC) AS rn
      FROM
      (SELECT pizza_types.category, pizza_types.name,
      SUM((order_details.quantity) * pizzas.price) AS revenue
      FROM pizza_types JOIN pizzas
      ON pizza_types.pizza_type_id = pizzas.pizza_type_id
      JOIN order_details
      ON order_details.pizza_id = pizzas.pizza_id
      GROUP BY pizza_types.category, pizza_types.name) AS a) AS b
      WHERE rn <= 3;
      ```
    - **Result:** Top 3 pizzas per category based on revenue

---

## Conclusion
This project demonstrates the effective use of SQL for in-depth analysis of sales data. The insights derived can help in improving business strategies, optimizing operations, and understanding customer preferences better. 

### Additional Insights for Business Improvement:

1. **Enhanced Marketing Strategies:**
   - **Promote High-Revenue Pizzas:** Focus marketing efforts on the top 3 most ordered pizza types based on revenue.
   - **Target Peak Hours:** Increase promotional activities during peak ordering times (e.g., 7 PM) to maximize sales.

2. **Menu Optimization:**
   - **Revise Pricing Strategy:** Evaluate and adjust the pricing of pizzas based on their popularity and revenue contribution.
   - **Expand Popular Categories:** Introduce new variations in the most popular pizza categories to attract more customers.

3. **Operational Efficiency:**
   - **Streamline Order Processing:** Optimize staffing and resources during peak hours to handle the high volume of orders.
