# postgresql

## Примеры запросов в PostgreSQL

#### Имеются три таблицы. Запросы для создания: 

### 1. Таблица Works

```sql
CREATE TABLE Works (
    workld serial not null,
    employeeName VARCHAR,
    roleEmployee VARCHAR,
    projectName VARCHAR,
    workload INT,
PRIMARY KEY(workld)
);
```
где **workload** - объем работы (рабочая нагрузка), измеряется в %

### 2. Таблица Projects

```sql
CREATE TABLE Projects (
    projectld serial not NULL,
    projectName varchar,
    productOwner varchar,
    duration int,
PRIMARY KEY(projectld)
);
```
где **duration** длительность проекта, измеряется в месяцах.

### 3. Таблица Employees

```sql
CREATE TABLE Employees (
    empld serial not NULL,
    employeeName varchar,
    roleEmployee varchar,
    place varchar,
    phone varchar,
PRIMARY KEY (empld)
);
```
### Запросы для заполнения таблиц данными: 

```sql
INSERT INTO Employees (empld, employeeName, roleEmployee, place, phone) VALUES
(default, 'Anna', 'Tester', 'Москва', '123-456-7890'),
(default, 'Leonid', 'Developer', 'Санкт-Петербург', '987-654-3210'),
(default, 'Martin', 'Tester', 'Казань', '555-555-5555'),
(default, 'Anna', 'Team Lead', 'Новосибирск', '111-222-3333');

INSERT INTO Projects (projectld, projectName, productOwner, duration) VALUES
(default, 'Deposit', 'External', 6),
(default, 'Credits', 'Internal', 12),
(default, 'Insurance', 'External', 3);

INSERT INTO Works (workld, employeeName, roleEmployee, projectName, workload) VALUES
(default, 'Anna', 'Tester', 'Deposit', 40),
(default, 'Leonid', 'Developer', 'Credits', 50),
(default, 'Anna', 'Team Lead', 'Insurance', 20),
(default, 'Martin', 'Tester', 'Credits', 100);
```


### Примеры запросов:

1. Вывести всех сотрудников с ролью 'Tester'.
есть возможность прикрепить файлы к ответу

```sql
SELECT employeename, workload 
FROM Works
WHERE roleemployee = 'Tester';
```

2. Вывести заказчиков, у которых длительность проекта больше
средней.
```sql
SELECT productowner
FROM projects
WHERE duration > (SELECT AVG(duration) FROM projects);
```
3. Вывести список всех сотрудников, заменив в значениях phone
все '-' на !!
```sql
SELECT empld, employeeName, roleEmployee, place,
REPLACE(phone, '-', '') AS phone
FROM Employees;
```
4. Получить отчет по сотрудникам с минимальной и
максимальной загрузкой.
Сортировать по максимальной загрузке по убыванию.
```sql
SELECT employeeName as "Имя сотрудника", MIN(workload) AS "Минимальная загрузка",
МАХ(workload) AS "Максимальная загрузка"
FROM Works
GROUP BY employeeName
ORDER BY "Максимальная загрузка" DESC;
```
5. Обновить данные для проекта с projectId равным 4, проставить наименование заказчика с "External" на "Internal".
```sql
UPDATE projects
SET productOwner = 'Internal'
WHERE projectid = 4; 

Проверить изменения 
SELECT * FROM projects;
```
6. Сгруппировать по названиям проектов и найти все группы,
для которых определена общая сумма объема работы более
30%.
```sql
SELECT projectName AS "Название проекта", SUM(workload) AS "Общая сумма объема работы"
FROM works
GROUP BY projectName
HAVING SUM(workload) > 30;
```
7. Вывести количество номеров телефонов сотрудников, у
которых есть телефон.
```sql
Select COUNT(phone) as "Количество номеров телефонов сотрудников"
From employees
WHERE phone IS NOT NULL;
```
8. Посчитать количество сотрудников на проектах с Длительностью от 2-х до 10-ти месяцев.
```sql
SELECT COUNT (DISTINCT w.employeeName) AS "Количество сотрудников"
FROM works AS w
JOIN projects AS pr ON w.projectName = pr.projectName
WHERE pr.duration BETWEEN 2 AND 10;
```
9. Вывести место сотрудника, где встречается цифра "6" в месте
сотрудника, и сотрудник работает 2 месяца.
```sql
SELECT empl.place FROM Employees AS empl
JOIN Works AS w ON empl.employeeName = w.employeeName
JOIN Projects AS pr ON w.projectName = pr.projectName WHERE empl.place LIKE '%6%'
AND pr.duration = 2;
```