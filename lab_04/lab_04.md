### Лабораторная работа 4. Оконные функции для анализа данных
## Вариант 4
## Цель работы 
Изучить концепцию оконных функций в SQL и научиться применять их для выполнения сложных аналитических расчетов: ранжирования, вычисления скользящих средних, нарастающих итогов и сравнительного анализа строк без группировки данных.
---

**Задание 1**
Ранжировать дилеров (dealerships) по дате открытия внутри каждого штата.
```sql
SELECT 
    state,
    dealership_id,
    date_opened,
    RANK() OVER (PARTITION BY state ORDER BY date_opened) AS rank_in_state
FROM dealerships;
```
Результат:

<img width="631" height="434" alt="Снимок экрана 2026-03-20 в 09 09 18" src="https://github.com/user-attachments/assets/69f0a70c-a0c5-4325-a728-21ab42835d4f" />

---

**Задание 2**
Для каждой продажи найти разницу в сумме с предыдущей продажей того же сотрудника (используйте LAG в таблице sales).
```sql
select 
    customer_id, 
    sales_transaction_date, 
    sales_amount,
    sales_amount - lag(sales_amount) over (
        partition by customer_id 
        order by sales_transaction_date
    ) as diff
from sales
limit 20;
```
Результат:

<img width="656" height="434" alt="Снимок экрана 2026-03-20 в 09 11 02" src="https://github.com/user-attachments/assets/37040936-65e9-48ab-947b-6621d13df273" />

---

**Задание 3**
Нарастающий итог количества отправленных писем (emails) по датам (sent_date).
```sql
with daily_emails as (
    select 
        sent_date::date as pure_date, 
        count(*) as daily_count
    from emails
    group by sent_date::date
)
select 
    pure_date as sent_date, 
    daily_count,
    sum(daily_count) over (
        order by pure_date
    ) as running_total
from daily_emails
order by sent_date;
```
Результат:

<img width="477" height="336" alt="Снимок экрана 2026-03-20 в 09 13 02" src="https://github.com/user-attachments/assets/61362944-205a-4bcd-a842-4116635c1ab1" />

---

## Вывод
В результате выполнения лабораторной работы я изучил структуру предложения OVER() и научился использовать ключевые слова PARTITION BY для группировки и ORDER BY для упорядочивания внутри окна, разобрался в различиях между ROW_NUMBER(), RANK() и DENSE_RANK(), что критически важно для построения рейтингов и топ-списков,
освоил LAG() и LEAD(), которые значительно упрощают межстрочные вычисления по сравнению с самосоединениями таблиц, научился применять агрегатные функции в оконном режиме для построения накопительных итогов и скользящих средних. Понимание оконного фрейма (ROWS BETWEEN) позволило гибко настраивать границы окна для вычислений.

