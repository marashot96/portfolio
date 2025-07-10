<div align="center"> 
       <a href="https://github.com/marashot96/portfolio/blob/main/SQL/Navigator.md#%EF%B8%8F-структура-раздела">🔙 Назад к навыкам в SQL </a> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
       <a href="https://github.com/marashot96/portfolio"> 🇬🇧 English version </a> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
       <a href="https://github.com/marashot96/portfolio/blob/main/README.md#-навыки">🔙 Назад к портфолио </a>  
</div> 

# <div align="center"> Расширенные SQL-запросы с оконными функциями </div>

Демонстрация продвинутого владения SQL с использованием оконных функций и CTE (Common Table Expressions). Примеры основаны на данных транзакций, предположительно, из модели PaySim.

---

## ✅ Запрос 1: Количество заказов и доля крупных заказов

### **Условие**  
Посчитать общее количество заказов в таблице `orders`, количество заказов с пятью и более товарами и найти долю таких заказов в общем количестве. Это важно для анализа поведения покупателей и выявления крупных корзин.

### **Запрос**
```sql
SELECT 
    COUNT(order_id) AS orders,
    COUNT(order_id) FILTER (WHERE array_length(product_ids, 1) >= 5) AS large_orders,
    ROUND(
        COUNT(order_id) FILTER (WHERE array_length(product_ids, 1) >= 5)::DECIMAL / COUNT(order_id), 
        2
    ) AS large_orders_share
FROM orders;
```

### **Результат**
| orders | large_orders | large_orders_share |
|--------|--------------|--------------------|
| 59595  | 11498        | 0.19               |

---

## 🔍 Запрос 2: Средний интервал между транзакциями клиента

### **Условие**  
Для каждого клиента рассчитать средний интервал между его транзакциями по дням. Дополнительно вывести общее число транзакций. Это позволяет понять, какие клиенты совершают операции часто, а какие — редко.

### **Запрос**
```sql
WITH transaction_deltas AS (
    SELECT nameOrig AS client_id,
           DATE(timestamp) AS date,
           timestamp,
           LAG(timestamp) OVER (PARTITION BY nameOrig ORDER BY timestamp) AS prev_time
    FROM transactions
),
intervals AS (
    SELECT client_id,
           date,
           EXTRACT(EPOCH FROM timestamp - prev_time) AS interval_seconds
    FROM transaction_deltas
    WHERE prev_time IS NOT NULL
),
aggregated AS (
    SELECT client_id,
           COUNT(*) + 1 AS total_transactions,
           ROUND(AVG(interval_seconds) / 60, 2) AS avg_interval_minutes
    FROM intervals
    GROUP BY client_id
)
SELECT *
FROM aggregated
ORDER BY avg_interval_minutes ASC
LIMIT 20;
```

### **Результат** (пример):

| client_id   | total_transactions | avg_interval_minutes |
|-------------|--------------------|-----------------------|
| C191011205  | 15                 | 8.45                  |
| C320199823  | 8                  | 10.33                 |
| C513000184  | 20                 | 12.91                 |

---

## 🏆 Запрос 3: Топ клиентов по переводам в будни и выходные

### **Условие**  
Рассчитать дневной рейтинг клиентов по объему переводов (`type = 'TRANSFER'`) отдельно по будням и выходным. Вывести топ-5 клиентов по каждому дню и сегменту.

### **Запрос**
```sql
WITH daily_transfers AS (
    SELECT nameOrig AS client_id,
           DATE(timestamp) AS day,
           CASE 
               WHEN EXTRACT(DOW FROM timestamp) IN (0,6) THEN 'weekend' 
               ELSE 'weekday' 
           END AS day_type,
           SUM(amount) AS total_amount
    FROM transactions
    WHERE type = 'TRANSFER'
    GROUP BY nameOrig, DATE(timestamp), EXTRACT(DOW FROM timestamp)
),
ranked AS (
    SELECT *,
           RANK() OVER (PARTITION BY day, day_type ORDER BY total_amount DESC) AS daily_rank
    FROM daily_transfers
)
SELECT *
FROM ranked
WHERE daily_rank <= 5
ORDER BY day, day_type, daily_rank;
```

### **Результат** (пример):

| client_id   | day        | day_type | total_amount | daily_rank |
|-------------|------------|----------|--------------|-------------|
| C1001       | 2025-01-01 | weekday  | 310000.00    | 1           |
| C1019       | 2025-01-01 | weekday  | 285000.00    | 2           |
| C1212       | 2025-01-02 | weekend  | 345000.00    | 1           |

---

## 💼 Контактная информация
Если вы хотите обсудить различные задачи, запросы или проекты, предложить кейс или сотрудничество — обязательно напишите мне!

- 📫 [t.me/marashot96](https://t.me/marashot96)
- 🌐 [marashot96@ya.ru](mailto:marashot96@ya.ru)


<div align="center"> 
       <a href="https://github.com/marashot96/portfolio/blob/main/SQL/Navigator.md#%EF%B8%8F-структура-раздела">🔙 Назад к навыкам в SQL </a> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
       <a href="https://github.com/marashot96/portfolio/blob/main/README.md#-навыки">🔙 Назад к портфолио </a>  
</div> 
