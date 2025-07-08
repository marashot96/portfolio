<div align="center"> 
       <a href="https://github.com/marashot96/portfolio/blob/main/SQL/Navigation.md">🔙 Назад к навыкам в SQL </a> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
       <a href="https://github.com/marashot96/portfolio"> 🇬🇧 English version </a> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
       <a href="https://github.com/marashot96/portfolio/blob/main/README.md#-реализованные-проекты">🔙 Назад к портфолио </a>  
</div> 

# <div align='center'> Фильтрация, сортировка, группировка  </div>

## 🧭 Описание

<p align='justify'> В этом разделе собраны запросы, демонстрирующие уверенное владение SQL: агрегация, фильтрация, оконные и условные конструкции, работа с датами, массивами и типами `timestamp`. Эти задачи решают реальные аналитические кейсы: от расчёта показателей конверсии до оценки структуры заказов и симуляции ценовой политики. </p>

Навыки, которые демонстрируются в запросах раздела:

- Группировка и агрегации по условиям (`GROUP BY`, `FILTER`, `CASE`).
- Работа с датами и временем (`date_part`, `to_char`, `timestamp`).
- Использование массивов и их свойств (`array_length`).
- Сложные `CASE`-конструкции и бизнес-правила.
- Сортировка и фильтрация с бизнес-логикой.
- Аналитический подход к структуре заказов и действиям пользователей.

---

## 🔍 Запросы

### 1. Доля доставленных заказов (в разрезе дней недели)

#### Условие:
Рассчитать число созданных, отменённых и доставленных заказов, а также **долю доставленных заказов** (успешных) для каждого дня недели за период с 24 августа по 6 сентября 2022 года. Группировка — по сокращённому названию и порядковому номеру дня недели.

#### Запрос:
```sql
SELECT 
    date_part('isodow', time)::integer AS weekday_number,
    to_char(time, 'Dy') AS weekday,
    COUNT(order_id) FILTER (WHERE action LIKE 'cr%') AS created_orders,
    COUNT(order_id) FILTER (WHERE action LIKE 'cancel%') AS canceled_orders,
    COUNT(order_id) FILTER (WHERE action LIKE 'cr%') - COUNT(order_id) FILTER (WHERE action LIKE 'cancel%') AS actual_orders,
    ROUND(
        (COUNT(order_id) FILTER (WHERE action LIKE 'cr%') - COUNT(order_id) FILTER (WHERE action LIKE 'cancel%')) 
        / COUNT(order_id) FILTER (WHERE action LIKE 'cr%')::DECIMAL, 
        3
    ) AS success_rate
FROM user_actions
WHERE time BETWEEN '2022-08-24' AND '2022-09-07'
GROUP BY 1, 2
ORDER BY 1;
```
#### Результат запроса 1:

| weekday_number | weekday | created_orders | canceled_orders | actual_orders | success_rate |
|----------------|---------|----------------|-----------------|---------------|--------------|
| 1              | Mon     | 8374           | 434             | 7940          | 0.948        |
| 2              | Tue     | 7193           | 370             | 6823          | 0.949        |
| 3              | Wed     | 3758           | 210             | 3548          | 0.944        |
| 4              | Thu     | 5004           | 258             | 4746          | 0.948        |
| 5              | Fri     | 6800           | 352             | 6448          | 0.948        |
| 6              | Sat     | 8249           | 399             | 7850          | 0.952        |
| 7              | Sun     | 9454           | 443             | 9011          | 0.953        |

<p> <i> Количество строк: 7 </i> </p> 

---

### 2. Отмены заказов по средам в августе

#### Условие:
Выбрать все отмены заказов, совершённые **по средам** в августе 2022 года с **12:00 до 15:59**. Вывести user_id, order_id, тип действия и дату. Сортировка — по убыванию ID заказа.

#### Запрос:
```sql
SELECT 
    user_id,
    order_id,
    action,
    time
FROM user_actions
WHERE 
    action = 'cancel_order'
    AND date_part('year', time) = 2022
    AND date_part('month', time) = 8
    AND date_part('dow', time) = 3
    AND date_part('hour', time) BETWEEN 12 AND 15
ORDER BY order_id DESC;
```
#### Результат запроса 2 (фрагмент):

| user_id | order_id | action       | time           |
|---------|----------|--------------|----------------|
| 8117    | 17307    | cancel_order | 31/08/22 15:59 |
| 8107    | 17282    | cancel_order | 31/08/22 15:50 |
| 1495    | 17272    | cancel_order | 31/08/22 15:46 |
| ...        | ...             | ...       | ... |

<p> <i> Количество строк: 36 </i> </p>

---

### 3. Средний размер заказа: будни vs. выходные

#### Условие:
Посчитать **среднее количество товаров в заказе** по будням и выходным. Будни — понедельник–пятница, выходные — суббота и воскресенье. Вывести две колонки: `week_part`, `avg_order_size`. Округлить до двух знаков. Сортировка — по возрастанию среднего размера заказа.

#### Запрос:
```sql
SELECT 
    CASE 
        WHEN date_part('dow', creation_time) BETWEEN 1 AND 5 THEN 'weekdays'
        ELSE 'weekend'
    END AS week_part,
    ROUND(AVG(array_length(product_ids, 1)), 2) AS avg_order_size
FROM orders
GROUP BY 1
ORDER BY avg_order_size;
```
#### Результат запроса 3:

| week_part | avg_order_size |
|-----------|----------------|
| weekend   | 3.39           |
| weekdays  | 3.40           |

<p> <i> Количество строк: 2 </i> </p>

---

### 4. Повышение цен: только нужным товарам

#### Условие:
Повысить цену **на 5%**, но только если:
- цена > 100 ₽,
- цена < 800 ₽,
- и это не икра (указано косвенно: икра стоит ровно 800 ₽).

Вывести `product_id`, `name`, `old_price`, `new_price`. Без округления. Сортировка: по убыванию новой цены, затем по возрастанию ID.

#### Запрос:
```sql
SELECT 
    product_id,
    name,
    price AS old_price,
    CASE 
        WHEN price > 100 AND price < 800 THEN 1.05 * price
        ELSE price
    END AS new_price
FROM products
ORDER BY new_price DESC, product_id;
```

#### Результат запроса 4 (фрагмент):

| product_id | name            | old_price | new_price |
|------------|-----------------|-----------|-----------|
| 13         | икра            | 800.0     | 800.0     |
| 37         | баранина        | 559.0     | 586.95    |
| 15         | масло оливковое | 450.0     | 472.5     |
| ...        | ...             | ...       | ...       |
| 73         | лепешка         | 15.0      | 15.0      |
| 10         | семечки         | 12.0      | 12.0      |
| 54         | пакет бумажный  | 1.0       | 1.0       |

<p> <i> Количество строк: 87 </i> </p>

---

## 💼 Контактная информация
Если вы хотите обсудить различные задачи, запросы или проекты, предложить кейс или сотрудничество — обязательно напишите мне!

- 📫 [t.me/marashot96](https://t.me/marashot96)
- 🌐 [marashot96@ya.ru](mailto:marashot96@ya.ru)


<div align="center"> 
       <a href="https://github.com/marashot96/portfolio/blob/main/SQL/Navigation.md">🔙 Назад к навыкам в SQL </a> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
       <a href="https://github.com/marashot96/portfolio/blob/main/README.md#-реализованные-проекты">🔙 Назад к портфолио </a>  
</div> 
