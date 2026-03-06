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
Создаю таблицу scooters_update с товарами типа scooter.
```sql
CREATE TABLE scooters_update AS
SELECT *
FROM products
WHERE product_type = 'scooter';
```
Уменьшаю цену на $50.
```sql
UPDATE scooters_update
SET base_msrp = base_msrp - 50;
```
<img width="334" height="205" alt="image" src="https://github.com/user-attachments/assets/a67ca763-99fe-4542-838d-aad6a95c9936" />

Удаляю модели 'Lemon'.
```sql
DELETE FROM scooters_update
WHERE model = 'Lemon';
```
<img width="1122" height="205" alt="image" src="https://github.com/user-attachments/assets/48c0cfce-e422-4b99-9549-7436d67fc65b" />


---

# Вывод
В ходе лабораторной работы я изучил основные операции SQL для работы с базами данных. Я выполнил запросы на выборку данных с использованием условий фильтрации и сортировки. Также были выполнены операции изменения данных (CRUD): создание новой таблицы, обновление значений и удаление записей. Полученные навыки позволяют мне эффективно выполнять анализ данных и безопасно работать с изменением данных в локальной среде.
