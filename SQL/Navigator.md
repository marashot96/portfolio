<div align="center">
  🇬🇧 <a href="/SQL/Navigator-EN.md">English version</a> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
  <a href="https://github.com/marashot96/portfolio/blob/main/README.md#-навыки"> ⬅️ Назад к портфолио</a>
</div>

# <div align='center'> Навыки работы в SQL </div>

Добро пожаловать в раздел SQL моего портфолио! Здесь представлены практические задачи, которые демонстрируют мой уровень владения SQL при работе с реальными и учебными датасетами.

## 📌 Основные навыки

- Уверенное владение **запросами SELECT, WHERE, GROUP BY, ORDER BY**
- Опыт написания **JOIN’ов всех типов (INNER, LEFT, FULL)**
- Умение использовать **вложенные запросы и временные таблицы (WITH)**
- Работа с **агрегациями и оконными функциями (RANK, ROW_NUMBER, SUM OVER)**
- Подготовка аналитических выборок, временных таблиц, фильтрации и ранжирования данных


## 🗂️ Структура раздела
Здесь приведены различные запросы, которые я использовал в работе на протяжении последных 5 лет. Сделал их основную сборку.

| № | Файл | Инструменты | Описание |
|---|------|------------|---------|
| 1 | [`basic select`](/SQL/basic-select.md) | Фильтрация, сортировка, группировка | Показаны простейшие запросы, которые можно использовать для первичного анализа датасета |
| 2 | [`joins and subqueries`](/SQL/join-subqueries.md) | Соединения таблиц, подзапросы, временные таблицы | Более сложный анализ, с использованием всех видов соединения таблиц и подзапросов |
| 3 | [`agg`](/SQL/agg.md) | Агрегации и оконные функции | Поиск необходимых для аналитики метрик, агрегация, работа с датой/временем и оконных функций |
| 4 | [`PaySim case`](/SQL/PaySim%20case.md) | Сочетание различных инструментов | На примере кейса про анализ клиентской активности в онлайн-банке (по данным PaySim) показано применение сложных SQL-запросов. ТАкже на основе этих запросов сделаны аналитические выводы. |

Ниже приведен один из примеров.

---

## ▶️ Пример моего запроса

### Суть запроса:
Вывести последние 3 транзакции каждого клиента

### Инстументы:
Join, оконные функции, подзапросы

### Запрос:
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

## 🧩 Используемые СУБД
- PostgreSQL (основная)
- SQLite (для быстрого прототипирования)
- MySQL (на этапе тестирования)

---

## 💼 Контактная информация
Если вы хотите обсудить различные задачи, запросы или проекты, предложить кейс или сотрудничество — обязательно напишите мне!

- 📫 [t.me/marashot96](https://t.me/marashot96)
- 🌐 [marashot96@ya.ru](mailto:marashot96@ya.ru)

<div align='center'> <a href="https://github.com/marashot96/portfolio/blob/main/README.md#-навыки"> ⬅️ Назад к портфолио</a> </div>
