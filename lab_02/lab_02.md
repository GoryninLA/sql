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
