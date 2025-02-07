### Встраиваемые и клиент-серверные СУБД

1. Встраиваемые СУБД
- Состоят из одного файла на диске,
- Нет системы пользователей,
- Работают очень быстро, так как это по факту просто файл на диске,
- Плохо масштабируемы,
- Решение для тестирования или для работы клиента с локальными

Пример - SQLite.

2. Клиент-Серверные СУБД
- Состоят из клиента и сервера,
- Разграничение доступа между пользователями,
- Клиент-серверная технология, то есть будут сетевые задержки,
- Легко масштабируемы,
- Возможна запись больших объемов данных

Пример - MS SQL Server, Oracle, PostgreSQL.

#### Реляционные базы данных:
Таблица - набор элементов данных, использующий модель вертикальных столбцов и горизонтальных строк.
Ячейка - место, где строка и столбец пересекаются.

Таблица содержит определенное число столбцов, но может иметь любое количество строк.

Типы данных:
1. Boolean
2. Character string (char(n)) - 1 byte
3. Varchar string (varchar(n))
4. Text string (text)
5. Small Integer (smallint) - 2 bytes
6. Integer (int) - 4 bytes
7. Big Integer (bigint) - 8 bytes
8. Serial (serial) - 4 bytes
9. Float integer (float(n)) - 8 bytes
10. Float integer (float8 Or real) - 4 bytes 
11. Real Number (numecric Or numeric(p,s))
12. Date (date)
13. Time (time)
14. Timestamp (timestamp)
15. TIMESTAMPTZ (timestamptz)
16. INTERVAL (interval)
#### Не реляционные базы данных:




### SQL
Язык программирования структурированных запросов (SQL, Structured Query Language), который используется в качестве эффективного способа сохранения данных, поиска их частей, обновления, извлечения из базы и удаления.

SQL состоит из 4-х языков:
1. DDL - Data Definition Language:
Группа операторов определения данных. Другими словами, с помощью операторов, входящих в эти группы, мы определяем структуру базы данных и работаем с объектами этой базы, т.е. создаем, изменяем и удаляем их.
В эту группу входят операторы:
- CREATE - используется для создания объектов базы данных,
- ALTER - используется для изменения объектов базы данных,
- DROP - используется для удаления объектов базы данных.

2. DML - Data Manipulation Language:
Группа операторов для манипуляции данными. С помощью этих операторов мы можем добавлять, изменяться, удалять и выгружать данные из базы, т.е. манипулировать ими.
В эту группу входят самые распространенные операторы языка SQL:
- SELECT - осуществляет выборку данных,
- INSERT - добавляет новые данные,
- UPDATE - изменяет существующие данные,
- DELETE - удаляет данные.

3. DCL - Data Control Language:
Группа операторов определения доступа к данным. Иными словами, это операторы для управления разрешениями, с помощью них мы можем разрешать или запрещать выполнение определенных операций над объектами базы данных.
Сюда входят:
- GRANT - предоставляет пользователю или группе разрешение на определенные операции с объектом,
- REVOKE - отзывает выданные разрешения.

5. TCL - Transaction Control Language:
Группа операторов для управления транзакциями (поддерживается не везде).
Транзакция - команда или блок команд (инструкций), которые успешно завершаются как единое целое, при этом в базе данных все внесенные изменения фиксируются на постоянной основе или отменяются, т.е. все изменения, внесенные любой командой, входящей в транзакцию, будут отменены.

Группа операторов TCL предназначена как раз для реализации и управления транзакциями. Сюда можно отнести:
- BEGIN TRANSACTION - служит для определения начала транзакции,
- COMMIT TRANSACTION - применяет транзакцию,
- ROLLBACK TRANSACTION - откатывает все изменения, сделанные в контексте текущей транзакции,
- SAVEPOINT - устанавливает промежуточную точку сохранения внутри транзакции.


### Работа с таблицами:
```
CREATE TABLE table_name
(
	column1 datatype,
	column2 datatype,
	column3 datatype,
	...
);

CREATE TTABLE Person
(
	PersonID int,
	LastName varchar(255),
	FirstName varchar(255),
	Address varchar(255),
	City varchar(255)
);

// Alter Table & Rename Column & Rename Table
// изменение таблицы
select * from emp
alter table employee_clone add column dept_id integer;
alter table employee_clone alter column dept_id type numeric;
alter table employee_clone rename column dept_id to dept
alter table employee_clone drop column dept
alter table employee_clone add column dept integer default 1;
alter table employee_clone add primary key (empid);
alter table employee_clone add column Age int check (Age > 0) default 20
alter table employee_cl rename to emp;

DROP TABLE table_name;
DROP TABLE Shippers;
```

Работа с данными:

```
// Вставка данных
INSERT INTO <table_name> (<column_1>, <column_2>, ... <column_n>)
VALUES
	(<value1_1>,<value1_2>, ... <value1_n>),
	(<value2_1>,<value2_2>, ... <value2_n>),
	...
	(<value3_1>,<value3_2>, ... <value3_n>);

// Выборка данных
SELECT
	<field_1>,
	<field_2>,
	...
	<field_n>
FROM
	<table_name>
WHERE
	<condition>

// Изменение данных
UPDATE
	<table_name>
SET
	<column1> = <value1>,
	<column2> = <value2>...
WHERE
	<condition>


// Удаление
DELETE FROM
	<table_name>
WHERE
	<condition>;

```
