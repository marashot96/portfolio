<div align="center">
  🇷🇺 <a href="/SQL/Navigator.md">Russian version</a> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
  <a href="https://github.com/marashot96/portfolio/blob/main/README-EN.md#-skills"> ⬅️ Back to portfolio</a>
</div>

# <div align='center'> SQL Skills </div>

Welcome to the SQL section of my portfolio! Here you will find practical tasks demonstrating my proficiency in SQL working with real and educational datasets.

## 📌 Core Skills

- Confident use of **SELECT, WHERE, GROUP BY, ORDER BY** clauses  
- Experience writing **all types of JOINs (INNER, LEFT, FULL)**  
- Ability to use **subqueries and Common Table Expressions (WITH)**  
- Working with **aggregations and window functions (RANK, ROW_NUMBER, SUM OVER)**  
- Preparing analytical datasets, temporary tables, filtering, and ranking data

## 🗂️ Section Structure  
This is a curated collection of various queries I have used over the last 5 years in my work.

| # | File | Tools | Description |
|---|------|-------|-------------|
| 1 | [`basic select`](/SQL/basic-select.md) | Filtering, sorting, grouping | Basic queries suitable for initial dataset analysis |
| 2 | [`joins and subqueries`](/SQL/join-subqueries.md) | Table joins, subqueries, CTEs | More advanced analysis using all join types and subqueries |
| 3 | [`agg`](/SQL/agg.md) | Aggregations and window functions | Calculating key metrics for analytics, working with dates/times and window functions |
| 4 | [`PaySim case`](/SQL/PaySim%20case.md) | Combining various tools | A case study on customer activity analysis in an online bank (PaySim data), demonstrating complex SQL queries and resulting analytics |

Below is an example query.

---

## ▶️ Sample Query

### Query Purpose:  
Retrieve the last 3 transactions for each customer

### Tools Used:  
Join, window functions, subqueries

### Query:
```sql
WITH ranked_transactions AS (
    SELECT
        c.customer_id, c.name, t.transaction_id, t.amount, t.transaction_date,
        COUNT(*) OVER (PARTITION BY c.customer_id) AS total_transactions,
        ROW_NUMBER() OVER (PARTITION BY c.customer_id ORDER BY t.transaction_date DESC) AS txn_rank
    FROM customers c
    JOIN transactions t ON c.customer_id = t.customer_id
)

SELECT
    customer_id, name, transaction_id, amount, transaction_date, total_transactions, txn_rank
FROM ranked_transactions
WHERE txn_rank <= 3
ORDER BY customer_id, txn_rank;
```

## 🧩 Databases Used
- PostgreSQL (primary)
- SQLite (for rapid prototyping)
- MySQL (testing phase)

## 💼 Contact Information

If you'd like to discuss tasks, projects, or proposals — or suggest a case study or collaboration — feel free to reach out!

- 📫 [t.me/marashot96](https://t.me/marashot96)
- 🌐 [marashot96@ya.ru](mailto:marashot96@ya.ru)

<div align='center'> <a href="https://github.com/marashot96/portfolio/blob/main/README-EN.md#-skills"> ⬅️ Back to portfolio</a> </div> ```
