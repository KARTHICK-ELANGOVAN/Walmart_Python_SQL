# Walmart Data Analysis: SQL + Python Project

## Project Overview

![Project Pipeline](https://github.com/KARTHICK-ELANGOVAN/Walmart_Python_SQL/blob/main/walmart_project-piplelines.png)


This project is an end-to-end data analysis solution designed to extract critical business insights from Walmart sales data. We utilize Python for data processing and analysis, SQL for advanced querying, and structured problem-solving techniques to solve key business questions. The project is ideal for data analysts looking to develop skills in data manipulation, SQL querying, and data pipeline creation.

---

## Project Steps

### 1. Set Up the Environment
   - **Tools Used**: Visual Studio Code (VS Code), Python, SQL (MySQL and PostgreSQL)
   - **Goal**: Create a structured workspace within VS Code and organize project folders for smooth development and data handling.

### 2. Set Up Kaggle API
   - **API Setup**: Obtain your Kaggle API token from [Kaggle](https://www.kaggle.com/) by navigating to your profile settings and downloading the JSON file.
   - **Configure Kaggle**: 
      - Place the downloaded `kaggle.json` file in your local `.kaggle` folder.
      - Use the command `kaggle datasets download -d <dataset-path>` to pull datasets directly into your project.

### 3. Download Walmart Sales Data
   - **Data Source**: Use the Kaggle API to download the Walmart sales datasets from Kaggle.
   - **Dataset Link**: [Walmart Sales Dataset](https://www.kaggle.com/najir0123/walmart-10k-sales-datasets)
   - **Storage**: Save the data in the `data/` folder for easy reference and access.

### 4. Install Required Libraries and Load Data
   - **Libraries**: Install necessary Python libraries using:
     ```bash
     pip install pandas numpy sqlalchemy mysql-connector-python psycopg2
     ```
   - **Loading Data**: Read the data into a Pandas DataFrame for initial analysis and transformations.

### 5. Explore the Data
   - **Goal**: Conduct an initial data exploration to understand data distribution, check column names, types, and identify potential issues.
   - **Analysis**: Use functions like `.info()`, `.describe()`, and `.head()` to get a quick overview of the data structure and statistics.

### 6. Data Cleaning
   - **Remove Duplicates**: Identify and remove duplicate entries to avoid skewed results.
   - **Handle Missing Values**: Drop rows or columns with missing values if they are insignificant; fill values where essential.
   - **Fix Data Types**: Ensure all columns have consistent data types (e.g., dates as `datetime`, prices as `float`).
   - **Currency Formatting**: Use `.replace()` to handle and format currency values for analysis.
   - **Validation**: Check for any remaining inconsistencies and verify the cleaned data.

### 7. Feature Engineering
   - **Create New Columns**: Calculate the `Total Amount` for each transaction by multiplying `unit_price` by `quantity` and adding this as a new column.
   - **Enhance Dataset**: Adding this calculated field will streamline further SQL analysis and aggregation tasks.

### 8. Load Data into MySQL 
   - **Set Up Connections**: Connect to MySQL using `sqlalchemy` and load the cleaned data into each database.
   - **Table Creation**: Set up tables in MySQL using Python SQLAlchemy to automate table creation and data insertion.
   - **Verification**: Run initial SQL queries to confirm that the data has been loaded accurately.

### 9. SQL Analysis: Complex Queries and Business Problem Solving
   - ****Business Problem-Solving****: Write and execute complex SQL queries to answer critical business questions, such as:
     - Revenue trends across branches and categories.
     - Identifying best-selling product categories.
     - Sales performance by time, city, and payment method.
     - Analyzing peak sales periods and customer buying patterns.
     - Profit margin analysis by branch and category.
   - **Documentation**: Keep clear notes of each query's objective, approach, and results.
   
   This section includes MySQL queries solving real-world business problems from a retail dataset (Walmart), focusing on revenue, customer behavior, ratings, and sales performance.

#### 📌 Q1: Find different payment methods, number of transactions, and quantity sold

**Objective:** Understand payment preferences and volume sold.

```sql
SELECT 
    payment_method, 
    COUNT(*) AS no_payment, 
    SUM(quantity) AS no_of_quan_sold
FROM walmart
GROUP BY payment_method;
```
Result:

![q1](https://i.ibb.co/WvWMk3BL/Screenshot-2025-07-18-010722.png)

**Insight:** Helps the business optimize for the most-used payment methods and improve transaction efficiency.

#### 📌 Q2: Which product category has the highest average rating in each branch?

**Objective:** Identify top-rated categories by customer satisfaction in each branch.

**Explanation:**
Ranks average product ratings for each category within each branch using the RANK() function.

```sql
SELECT *
FROM (
    SELECT  
        branch, 
        category, 
        AVG(rating) AS avg_rating,
        RANK() OVER(PARTITION BY branch ORDER BY AVG(rating) DESC) AS rank_
    FROM walmart 
    GROUP BY branch, category
) AS rank_of_avg_rating
WHERE rank_ = 1;
```
Result:

![q2](https://i.ibb.co/PzjjB6tC/Screenshot-2025-07-18-010806.png)

**Insight:**
Highlights customer-preferred categories per location, aiding targeted inventory and promotions.

#### 📌 Q3: What is the busiest day for each branch?

**Explanation:**
Groups transactions by weekday per branch and ranks them to find the highest.

```sql
SELECT *
FROM (
    SELECT 
        branch,
        DAYNAME(STR_TO_DATE(date, '%d/%m/%Y')) AS day_name,
        COUNT(*) AS no_transactions,
        RANK() OVER(PARTITION BY branch ORDER BY COUNT(*) DESC) AS rank_
    FROM walmart
    GROUP BY branch, day_name
) AS rank_of_trans
WHERE rank_ = 1;
```
Result:

![q3](https://i.ibb.co/VY4kZFBc/Screenshot-2025-07-18-010828.png)

**Insight:**
Ideal for staffing decisions, promotions, and understanding peak customer activity.

#### 📌 Q4: What is the total quantity sold for each payment method?

**Explanation:**
Sums up all quantities sold per payment method.

```sql
SELECT 
    payment_method, 
    SUM(quantity) AS quantity_sold
FROM walmart
GROUP BY payment_method;
```
Result:

![q4](https://i.ibb.co/KxQwSgdR/Screenshot-2025-07-18-010847.png)

**Insight:**
Reveals which payment types tend to result in bulk buying behavior.

#### 📌 Q5: What is the average, min, and max rating for each category in each city?

**Explanation:**
Aggregates rating stats for each category in every city.

```sql
SELECT 
    city,
    category,
    AVG(rating) AS avg_rating, 
    MIN(rating) AS min_rating, 
    MAX(rating) AS max_rating 
FROM walmart 
GROUP BY city, category;
```

Result:

![q5](https://i.ibb.co/JWRwxYMC/Screenshot-2025-07-18-010912.png)

**Insight:**
Identifies cities where certain product categories are underperforming or excelling.

#### 📌 Q6: What is the total profit earned from each category?

**Explanation:**
Calculates total profit per category using unit price, quantity, and profit margin.

```sql
SELECT 
    category,
    SUM(unit_price * quantity * profit_margin) AS total_profit
FROM walmart
GROUP BY category
ORDER BY total_profit DESC;
```

Result:

![q6](https://i.ibb.co/0jyBS9wz/Screenshot-2025-07-18-011444.png)

**Insight:**
Guides investment in top-performing categories and reconsideration of low-profit ones.

#### 📌 Q7: What is the most commonly used payment method in each branch?
**Explanation:**
Ranks payment methods by transaction count for each branch.

```sql
WITH cte AS (
    SELECT 
        branch,
        payment_method,
        COUNT(*) AS total_trans,
        RANK() OVER(PARTITION BY branch ORDER BY COUNT(*) DESC) AS rank_
    FROM walmart
    GROUP BY branch, payment_method
)
SELECT branch, payment_method AS preferred_payment_method
FROM cte
WHERE rank_ = 1;
```
Result:

![q7](https://i.ibb.co/jk57xq1S/Screenshot-2025-07-18-011503.png)

**Insight:**
Supports branch-specific marketing and payment system improvements.

#### 📌 Q8: What are the most active sales shifts (Morning/Afternoon/Evening)?

**Explanation:**
Categorizes transactions into time-based shifts and counts invoices.

```sql
SELECT
    branch,
    CASE 
        WHEN HOUR(TIME(time)) < 12 THEN 'Morning'
        WHEN HOUR(TIME(time)) BETWEEN 12 AND 17 THEN 'Afternoon'
        ELSE 'Evening'
    END AS shift,
    COUNT(*) AS num_invoices
FROM walmart
GROUP BY branch, shift
ORDER BY branch, num_invoices DESC;
```
Result:

![q4](https://i.ibb.co/LhSVczc7/Screenshot-2025-07-18-011526.png)

**Insight:**
Helps schedule staff and promotions based on high-traffic hours.



### 10. Project Publishing and Documentation
   - **Documentation**: Maintain well-structured documentation of the entire process in Markdown or a Jupyter Notebook.
   - **Project Publishing**: Publish the completed project on GitHub or any other version control platform, including:
     - The `README.md` file (this document).
     - Jupyter Notebooks .
     - SQL query scripts.
     - Data files or steps to access them.

---

## Requirements

- **Python 3.8+**
- **SQL Databases**: MySQL
- **Python Libraries**:
  - `pandas`, `numpy`, `sqlalchemy`, `mysql-connector-python`,`pymysql`
- **Kaggle API Key** (for data downloading)

## Getting Started

1. Clone the repository:
   ```bash
   git clone <repo-url>
   ```
2. Install Python libraries:
   ```bash
   pip install -r requirements.txt
   ```
3. Set up your Kaggle API, download the data, and follow the steps to load and analyze.

---

## Project Structure

```plaintext
|-- data/                     # Raw data and transformed data
|-- sql_queries/              # SQL scripts for analysis and queries
|-- notebooks/                # Jupyter notebooks for Python analysis
|-- README.md                 # Project documentation
|-- requirements.txt          # List of required Python libraries
|-- main.py                   # Main script for loading, cleaning, and processing data
```
---

## Results and Insights

This section will include your analysis findings:
- **Sales Insights**: Key categories, branches with highest sales, and preferred payment methods.
- **Profitability**: Insights into the most profitable product categories and locations.
- **Customer Behavior**: Trends in ratings, payment preferences, and peak shopping hours.

## Future Enhancements

Possible extensions to this project:
- Integration with a dashboard tool (e.g., Power BI or Tableau) for interactive visualization.
- Additional data sources to enhance analysis depth.
- Automation of the data pipeline for real-time data ingestion and analysis.

---

## License

This project is licensed under the MIT License. 

---

## Acknowledgments

- **Data Source**: Kaggle’s Walmart Sales Dataset
- **Inspiration**: Walmart’s business case studies on sales and supply chain optimization.

---
