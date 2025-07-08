<div align="center"> 
       <a href="https://github.com/marashot96/portfolio/blob/main/SQL/Navigation.md">🔙 Назад к навыкам в SQL </a> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
       <a href="https://github.com/marashot96/portfolio"> 🇬🇧 English version </a> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
       <a href="https://github.com/marashot96/portfolio/blob/main/README.md#-реализованные-проекты">🔙 Назад к портфолио </a>  
</div> 


# <div align='center'> Соединения таблиц и подзапросы, временные таблицы </div>

## 🧭 Описание

В этом разделе собраны запросы, демонстрирующие владение **соединениями таблиц (JOIN)** и **вложенными подзапросами (subqueries)**. Эти конструкции позволяют объединять и фильтровать данные из нескольких источников, а также извлекать дополнительную информацию, опираясь на промежуточные вычисления и бизнес-логику.

Навыки, которые демонстрируются в запросах раздела:

- Соединения `INNER JOIN`, `LEFT JOIN`, `USING` и `ON`.
- Вложенные подзапросы в `SELECT`, `FROM` и `WHERE`.
- Конструкция `WITH` (CTE) для разбиения логики на шаги.
- Расчёты агрегатов и фильтрация по ним.
- Работа с множественными условиями соединения и фильтрации.
- Построение аналитических отчётов через комбинирование таблиц.

## 🔍 Запросы

### 1. Топ-10 самых покупаемых товаров

#### Условие:
Определите 10 самых популярных товаров в таблице `orders`. Популярность измеряется количеством появлений товара в заказах (учитываются повторы в заказах). Учитывать только **неотменённые** заказы. Вывести `product_id`, `times_purchased`. Сортировка — по возрастанию `product_id`.

#### Запрос:
```sql
WITH tw AS (
    SELECT unnest(product_ids) AS product_id
    FROM orders
    WHERE order_id NOT IN (
        SELECT order_id
        FROM user_actions
        WHERE action LIKE '%cancel%'
    )
)
SELECT product_id, times_purchased
FROM (
    SELECT product_id,
           COUNT(product_id) AS times_purchased
    FROM tw
    GROUP BY product_id
    ORDER BY COUNT(product_id) DESC
    LIMIT 10
) AS t2
ORDER BY 1;
```

#### Результат запроса 1:

| product_id | times_purchased |
|------------|------------------|
| 1          | 3915             |
| 20         | 3887             |
| 25         | 3604             |
| 34         | 3873             |
| 40         | 3932             |
| 65         | 3905             |
| 70         | 3549             |
| 74         | 3782             |
| 76         | 3889             |
| 77         | 3930             |

<p> <i> Количество строк: 10 </i> </p>

---

### 2. Суммарная стоимость заказа

#### Условие:
Рассчитайте суммарную стоимость каждого заказа. Вывести `order_id` и `order_price`. Сортировка — по возрастанию `order_id`. Вывести только первые **1000 строк** результата.

#### Запрос:
```sql
SELECT order_id,
       SUM(price) AS order_price
FROM (
    SELECT od.order_id AS order_id,
           pr.product_id AS product_id,
           pr.price AS price
    FROM (
        SELECT order_id,
               unnest(product_ids) AS product_id
        FROM orders
        ORDER BY 1
    ) AS od
    LEFT JOIN products AS pr USING (product_id)
) AS t1
GROUP BY 1
ORDER BY 1
LIMIT 1000;
```
#### Результат запроса 2:

| order_id | order_price |
|----------|--------------|
| 1        | 175.0        |
| 2        | 394.0        |
| 3        | 493.0        |
| 4        | 156.0        |
| ...      | ...          |
| 996      | 295.0        |
| 997      | 150.0        |
| 998      | 358.0        |
| 999      | 289.0        |
| 1000     | 69.0         |

<p> <i> Количество строк: 1000 </i> </p>


---

## 💼 Контактная информация
Если вы хотите обсудить различные задачи, запросы или проекты, предложить кейс или сотрудничество — обязательно напишите мне!

- 📫 [t.me/marashot96](https://t.me/marashot96)
- 🌐 [marashot96@ya.ru](mailto:marashot96@ya.ru)


<div align="center"> 
       <a href="https://github.com/marashot96/portfolio/blob/main/SQL/Navigation.md">🔙 Назад к навыкам в SQL </a> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
       <a href="https://github.com/marashot96/portfolio/blob/main/README.md#-реализованные-проекты">🔙 Назад к портфолио </a>  
</div> 
