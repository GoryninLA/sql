<img width="1122" height="205" alt="image" src="https://github.com/user-attachments/assets/3fc4ad65-5312-4f33-9bc8-fe4432a3e013" /><img width="334" height="205" alt="image" src="https://github.com/user-attachments/assets/4526ab94-789e-4e78-b2df-5a215247a82e" /># Лабораторная работа №1  
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
Обновление данных
Уменьшаем цену на $50.
```sql
UPDATE scooters_update
SET base_msrp = base_msrp - 50;
```
blob:https://web.telegram.org/f5e61733-592e-41f8-ba07-5c8c9ce11370<img width="334" height="205" alt="image" src="https://github.com/user-attachments/assets/b37596e9-8da0-4928-87b9-7adeb3d1a1b3" />
Удаление данных
Удаляем модели 'Lemon'.
```sql
DELETE FROM scooters_update
WHERE model = 'Lemon';
```
blob:https://web.telegram.org/09d3916b-d3c1-4c67-9365-6d7626587e23<img width="1122" height="205" alt="image" src="https://github.com/user-attachments/assets/e2e002c5-ecad-43b7-8c22-cef581e797c4" />

---

# Вывод
В ходе лабораторной работы я изучил основные операции SQL для работы с базами данных. Я выполнил запросы на выборку данных с использованием условий фильтрации и сортировки. Также были выполнены операции изменения данных (CRUD): создание новой таблицы, обновление значений и удаление записей. Полученные навыки позволяют мне эффективно выполнять анализ данных и безопасно работать с изменением данных в локальной среде.
