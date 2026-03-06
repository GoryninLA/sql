# Лабораторная работа №1  
## Основы работы с SQL-запросами: выборка и модификация данных

**СУБД:** PostgreSQL  
**Инструмент:** DBeaver / pgAdmin / psql  

---

# Цель работы

Научиться формировать SQL-запросы на выборку данных с использованием фильтрации, сортировки и логических операторов.  
Освоить операции манипулирования данными (CRUD — Create, Read, Update, Delete) в безопасной локальной среде.

---

# Вариант 4

## Задание 1 (SELECT + сортировка)

Получить товары (`products`) модели **'Model Chi'**.  
Отсортировать по **году выпуска**.


```sql
SELECT *
FROM products
WHERE model = 'Model Chi'
ORDER BY year;
```
<img width="635" height="78" alt="Снимок экрана 2026-03-06 в 09 09 42" src="https://github.com/user-attachments/assets/ed07fa67-3e0e-4264-b9d8-9807b43f9f79" />

---

## Задание 2 (Логика и фильтры)
Получить клиентов (customers) из штатов NY или NJ, добавленных в 2019 год
```sql
SELECT *
FROM customers
WHERE state IN ('NY','NJ')
AND EXTRACT(YEAR FROM date_added) = 2019;
```
<img width="637" height="247" alt="Снимок экрана 2026-03-06 в 09 09 28" src="https://github.com/user-attachments/assets/fe0e6657-0791-4f74-b194-a8104a9d4b76" />

---
## Задание 3 (CRUD — локально)
Работа выполняется на локальной копии базы данных.
Создаем таблицу scooters_update с товарами типа scooter.
```sql
CREATE TABLE scooters_update AS
SELECT *
FROM products
WHERE product_type = 'scooter';
```
Уменьшаем цену на $50.
```sql
UPDATE scooters_update
SET base_msrp = base_msrp - 50;
```
<img width="334" height="205" alt="image" src="https://github.com/user-attachments/assets/a67ca763-99fe-4542-838d-aad6a95c9936" />

Удаляем модели 'Lemon'.
```sql
DELETE FROM scooters_update
WHERE model = 'Lemon';
```
<img width="1122" height="205" alt="image" src="https://github.com/user-attachments/assets/48c0cfce-e422-4b99-9549-7436d67fc65b" />


---

# Вывод
В ходе лабораторной работы я изучил основные операции SQL для работы с базами данных. Я выполнил запросы на выборку данных с использованием условий фильтрации и сортировки. Также были выполнены операции изменения данных (CRUD): создание новой таблицы, обновление значений и удаление записей. Полученные навыки позволяют мне эффективно выполнять анализ данных и безопасно работать с изменением данных в локальной среде.

---
# Лабораторная работа №2  
## JOIN, подзапросы и преобразование данных  
**Вариант 4**

---

# Цель работы

Изучить методы объединения данных из нескольких таблиц с использованием операторов `JOIN`, научиться применять подзапросы для поиска данных и использовать функции преобразования данных (`CASE`, `COALESCE`, `CAST`).

---

## Задача 1 — JOIN
Показать все продажи (сумма и дата) для категории товаров scooter.
```sql
SELECT 
    s.sales_amount,
    s.sales_transaction_date
FROM sales s
INNER JOIN products p
    ON s.product_id = p.product_id
WHERE p.product_type = 'scooter';
```
<img width="832" height="496" alt="image" src="https://github.com/user-attachments/assets/dad12521-aac5-40af-80e4-e0b5add1fb6f" />

---

## Задача 2 — Подзапрос
Найти продавцов, работающих в дилерских центрах штата NY.
```sql
SELECT 
    first_name,
    last_name
FROM salespeople
WHERE dealership_id IN (
    SELECT dealership_id
    FROM dealerships
    WHERE state = 'NY'
);
```
<img width="648" height="220" alt="image" src="https://github.com/user-attachments/assets/5d5ca48a-5c5e-42b3-87d0-3f5c00738b02" />

---

## Задача 3 — Преобразование данных
Создать поле full_name и преобразовать postal_code в тип integer.
```sql
SELECT 
    first_name || ' ' || last_name AS full_name,
    postal_code::INTEGER AS postal_code
FROM customers;
```
<img width="376" height="365" alt="image" src="https://github.com/user-attachments/assets/857d69c5-671f-40fa-bc27-4f26a7f68f17" />

---

# Вывод
В ходе лабораторной работы были изучены методы объединения данных из нескольких таблиц с использованием операторов JOIN. Также были освоены подзапросы для поиска данных и функции преобразования данных. Полученные знания позволяют эффективно работать с реляционными базами данных и формировать аналитические выборки для дальнейшего анализа.

---

# Лабораторная работа 3. 
## Агрегации и аналитические функции.

---

# Цель работы
Научиться выполнять быстрый и эффективный анализ данных, используя возможности агрегирования SQL. Освоить синтаксис группировки данных, фильтрации групп и вычисления статистических показателей

---
# Вариант 4

## Задание 1
Найти самую раннюю и самую позднюю дату продажи в sales.

```sql
SELECT 
    MIN(sales_transaction_date) AS earliest_sale,
    MAX(sales_transaction_date) AS latest_sale
FROM sales;
```
<img width="383" height="50" alt="Снимок экрана 2026-03-06 в 10 31 33" src="https://github.com/user-attachments/assets/db0ea290-d0af-446c-9759-5bdead44b050" />

---

## Задание 2
Посчитать сумму продаж (sales_amount) по каждому каналу (channel).
```sql
SELECT 
    channel,
    SUM(sales_amount) AS total_sales
FROM sales
GROUP BY channel;
```
<img width="652" height="146" alt="image" src="https://github.com/user-attachments/assets/294627aa-262a-4e31-8c19-16680d3691e4" />

---
## Задание 3
Показать штаты, в которых зарегистрировано более 500 женщин-клиентов.
```sql
SELECT 
    state,
    COUNT(*) AS female_customers
FROM customers
WHERE gender = 'F'
GROUP BY state
HAVING COUNT(*) > 500;
```
<img width="339" height="252" alt="Снимок экрана 2026-03-06 в 10 34 32" src="https://github.com/user-attachments/assets/9abd929a-ab20-4c55-98a8-695015fc25c7" />

---
# Вывод 
В ходе выполнения лабораторной работы я изучил основные возможности агрегирования данных в SQL. Были использованы агрегатные функции (`MIN`, `MAX`, `SUM`, `COUNT`) для вычисления статистических показателей, а также операторы `GROUP BY` и `HAVING` для группировки и фильтрации данных.
В процессе работы я получил навыки анализа данных в базе данных, определения минимальных и максимальных значений, подсчёта суммарных показателей и выделения групп записей по заданным условиям. Освоенные методы позволяют быстро и эффективно выполнять аналитические запросы и получать необходимую информацию из больших объёмов данных.


