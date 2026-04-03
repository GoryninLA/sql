# Лабораторная работа 5. Оптимизация запросов с помощью индексов и анализа плана выполнения.
## Цель работы
Научиться анализировать производительность SQL-запросов, интерпретировать план выполнения (Query Plan) и оптимизировать работу базы данных с помощью различных типов индексов (B-tree, Hash).

---

## Задание 1 
Найти письма (emails) со статусом opened = 't'

```sql
EXPLAIN analyze SELECT email_id, customer_id, email_subject, opened, sent_date, opened_date
FROM emails
WHERE opened = 't';
```

<img width="767" height="131" alt="Снимок экрана 2026-04-03 в 09 11 35" src="https://github.com/user-attachments/assets/4a7ef320-de37-4a7f-ac55-cf0ea9939e14" />

Анализ запроса **без индекса**:
| Показатель | Значение | Что означает |
|------------|----------|--------------|
| Тип сканирования | `Seq Scan` | Полное сканирование всей таблицы |
| Всего строк | 418 158 | (83579 + 334579) строк просмотрено |
| Отфильтровано | 334 579 | 80% строк отброшено |
| Результат | 83 579 | Только 20% строк подошли |

Создаем индекс 
```sql
CREATE INDEX idx_emails_opened_hash ON emails USING HASH (opened);
```

<img width="1053" height="363" alt="image" src="https://github.com/user-attachments/assets/dd460edf-4e90-455b-a711-a2f189847f42" />

Анализ запроса **с индексом**:
| Метрика | До оптимизации | После оптимизации | Улучшение |
|---------|----------------|-------------------|-----------|
| Scan Type | Seq Scan | Bitmap Index Scan |  2.6x быстрее |
| Rows Examined | 418K | 83.6K |  5x меньше |
| Execution Time | 71.1 ms | 27.5 ms | меньше на 62% |
| Buffers | 10,698 | 5,631 | меньше на 47% |

---

## Задание 2
Оптимизировать поиск продаж (sales) за конкретную дату sales_transaction_date (за 2017-07-19).

```sql
EXPLAIN analyze SELECT 
    s.customer_id,
    s.product_id,
    s.sales_transaction_date,
    s.sales_amount,
    s.channel,
    c.first_name,
    c.last_name,
    c.email,
    p.product_type,
    p.base_msrp
FROM sales s
JOIN customers c ON s.customer_id = c.customer_id
JOIN products p ON s.product_id = p.product_id
WHERE DATE(s.sales_transaction_date) = '2017-07-19';
```

<img width="939" height="801" alt="image" src="https://github.com/user-attachments/assets/7b51e830-7110-4364-88a6-2fea31ae564d" />

Анализ запроса **без индекса**:
| Показатель | Значение | Что означает |
|------------|----------|--------------|
| **Тип сканирования sales** | `Seq Scan` | Полное сканирование всей таблицы продаж |
| **Всего строк в sales** | 37 711 | (35 + 37 676) строк просмотрено |
| **Отфильтровано из sales** | 37 676 | 99.9% строк отброшено (из-за DATE()) |
| **Результат из sales** | 35 | Только 0.09% строк подошли |
| **Тип сканирования customers** | `Seq Scan` | Полное сканирование всех клиентов |
| **Всего строк в customers** | 50 000 | Каждый JOIN проверен с каждой продажей |
| **Тип соединения** | `Hash Join` | Все строки customers загружены в память |
| **Buffers hit** | 402 | Найдено в кеше (хорошо) |
| **Buffers read** | 1 064 | Пришлось читать с диска (плохо) |
| **Execution Time** | 17.424 ms | Общее время выполнения |
| **Эффективность** | 0.09% | Только 35 из 37 711 строк продаж полезны |


Улучшаем запрос 
```sql
SELECT 
    s.customer_id,
    s.product_id,
    s.sales_transaction_date,
    s.sales_amount,
    s.channel,
    c.first_name,
    c.last_name,
    c.email,
    p.product_type,
    p.base_msrp,
FROM sales s
JOIN customers c ON s.customer_id = c.customer_id
JOIN products p ON s.product_id = p.product_id
WHERE s.sales_transaction_date >= '2017-07-19 00:00:00'
  AND s.sales_transaction_date < '2017-07-20 00:00:00';
```

Создаем индекс 
```sql
CREATE INDEX idx_sales_date_range 
ON sales (sales_transaction_date);
```

<img width="1280" height="742" alt="image" src="https://github.com/user-attachments/assets/df703ad9-d08e-4a36-ab1f-1ce9ac2111c3" />

Анализ запроса **с индексом**:
| Метрика | ДО (первый план) | ПОСЛЕ (текущий план) | Улучшение |
|---------|------------------|----------------------|-----------|
| **Тип сканирования sales** | `Seq Scan` | `Index Scan` |  **намного лучше** |
| **Просмотрено строк sales** | 37 711 | ~35 |  **в 1 077 раз меньше** |
| **Execution Time** | 17.424 ms | 5.433 ms |  **в 3.2 раза быстрее** |
| **Planning Time** | 1.342 ms | 0.248 ms | **в 5.4 раза быстрее** |
| **Использование индекса** |  НЕТ |  **ДА** |  **задание выполнено** |

---

## Вывод

В ходе выполнения лабораторной работы я научился анализировать производительность SQL-запросов с помощью команд EXPLAIN и EXPLAIN ANALYZE, интерпретировать план выполнения и выявлять проблемные места, такие как последовательное сканирование (Seq Scan). Я изучил, как функция на индексированной колонке (например, DATE(column)) делает индекс бесполезным, и освоил правильное написание диапазонных условий (column >= date AND column < date + 1). На практике я создал хеш-индекс для булевого поля и B-Tree индекс для даты, добившись использования Index Scan вместо Seq Scan. В результате время выполнения запроса к таблице emails сократилось с 71 мс до 27.5 мс (ускорение в 2.6 раза), а запрос к sales с JOIN нескольких таблиц ускорился с 17.4 мс до 5.4 мс за счёт применения idx_sales_date_range и устранения 
