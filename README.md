# Курс “SQL для анализа данных” - SkyPro

---

Курсовая работа выполнена в рамках обучения на курсе SkyPro

Ссылка на курс: https://sky.pro/courses/analytics/sql

## Задачи по проекту:
- смоделировать изменение балансов студентов онлайн-школы в 2016 году
- создать таблицу, где будут балансы каждого студента за каждый день
- изучить изменения балансов студентов, найти аномалии в данных,
составить список вопросов дата-инженерам и владельцам таблиц
- визуализировать, сколько всего уроков было на балансе всех студентов за каждый календарный день,
как это количество менялось под влиянием транзакций (оплат, начислений, корректирующих списаний)
и уроков (списаний с баланса по мере их прохождения)
- сделать выводы на основе визуализации

---

## 01. Структура базы данных skyeng_db

- **`students`** — данные о студентах (потенциальных, которые только оставили заявку, и тех, кто действительно оплатил обучение)
- **`orders`** — данные о заявках на обучение
- **`classes`** — данные об уроках
- **`payments`** — данные об оплате
- **`teachers`** — данные об учителях

---

## 02. SQL-запрос с описанием логики его выполнения

1. Узнаем, когда была первая транзакция для каждого студента,
начиная с этой даты, будем собирать его баланс уроков
2. Соберем таблицу с датами за каждый календарный день 2016 года
3. Узнаем, за какие даты имеет смысл собирать баланс для каждого студента
4. Найдем изменения балансов студентов, связанные с успешными транзакциями
5. Найдем баланс студентов, который сформирован только транзакциями
6. Найдем изменения балансов студентов, связанные с прохождением уроков
7. Найдем баланс студентов, который сформирован только прохождением уроков
8. Найдем общий баланс студентов, сформированный транзакциями и прохождением уроков
9. Посмотрим, как менялось общее количество уроков на балансе всех студентов.


## 03. Визуализация динамики общего баланса всех учеников онлайн-школы

- **`total_transaction_balance_change`** - изменение общего баланса под влиянием транзакций (оплат, начислений, корректирующих списаний)
- **`total_transaction_balance_change_cs`** - кумулятивная сумма изменений общего баланса под влиянием транзакций
- **`total_classes_balance_change`** - изменение общего баланса под влиянием уроков (списаний с баланса по мере их прохождения)
- **`total_classes_balance_change_cs`** - кумулятивная сумма изменений общего баланса под влиянием уроков
- **`total_balance`** - общее количество уроков на балансе всех студентов

## 04. Таблицы с итоговыми данными, выводы и вопросы по консистентности

Вопросы к дата-инженерам и владельцам таблиц:

1. В итоговой таблице balances много минусовых балансов. 
Также в таблице classes есть студенты, по которым нет успешных оплат в таблице payments в 2016 году (CTE users_wo_successful_paymnets), 
при этом для них в 2016 году успешно проводились регулярные уроки (CTE classes_wo_successful_paymnets). 
Есть проблемы с неконсистентностью данных? Данные об оплатах неполные? Или несвоевременно поступают?
2. В таблице classes много уроков, где время окончания уроков меньше, чем время его начала.
Как уроки могут иметь отрицательную продолжительность? Данные о времени начала и окончания некорректны?
3. В таблице classes у удаленных уроков нередко null в статусе вместо moved.
При удалении уроков не всегда проставляется информация о статусе?

Выводы на основе визуализации:

Общая сумма уроков на балансе учеников постепенно растет.
Имеется сезонность уроков по дням недели. Наибольшая активность с понедельника по четверг,
в пятницу спадает, в выходные - примерно в 2 раза ниже, чем в пиковые дни.
Среднее количество уроков практически все время росло в течение года от недели к неделе и от месяца к месяцу, 
было снижение во время майских праздников, летом и во второй половине декабря.
Имеется сезонность оплат уроков по дням недели. 
Пиковый день - понедельник, меньше всего оплат производят в выходные - на треть ниже, чем в понедельник.
Среднее количество оплаченных уроков довольно стабильно росло в течение года,
был спад в марте, июле и декабре.
