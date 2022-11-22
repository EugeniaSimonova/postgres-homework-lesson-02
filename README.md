# 02 - SQL и реляционные СУБД. Введение в PostgreSQL 

1. Создана ВМ Яндекс.
2. Проверяем и отключаем auto commit:
  ```
  \echo :AUTOCOMMIT
  \set AUTOCOMMIT OFF
  ```  
3. Создаем таблицу в первой сессии:

```
create table persons(id serial, first_name text, second_name text);
insert into persons(first_name, second_name) values('ivan', 'ivanov');
insert into persons(first_name, second_name) values('petr', 'petrov');
commit;<br/>
````

4. Смотрим текущий уровень изоляции: 
```
show transaction isolation level;

transaction_isolation 
-----------------------
read committed
```
5. Добавляем новую запись в первой сессии:

```
insert into persons(first_name, second_name) values('sergey', 'sergeev');

INSERT 0 1
```
6. Делаем `select * from persons` во второй сессии:

```
select * from persons;

 id | first_name | second_name 
----+------------+-------------
  1 | ivan       | ivanov
  2 | petr       | petrov
(2 rows)

```
Новую запись не видим, так как транзакция не завершена, а при уровне изоляции `read committed` SELECT видит только данные, которые были закоммичены до начала выполнения SELECT.

7. Завершаем транзакцию в первой сессии `commit;` и выполняем тот же запрос во второй:

```
postgres=#  select * from persons;
 id | first_name | second_name 
----+------------+-------------
  1 | ivan       | ivanov
  2 | petr       | petrov
  3 | sergey     | sergeev
(3 rows)
```
Новую запись видим,так как транзакция завершена.

8. начинаеми транзакцию на другом уровне изоляции в первой сессии:

```
BEGIN TRANSACTION ISOLATION LEVEL REPEATABLE READ;
insert into persons(first_name, second_name) values('sveta', 'svetova');
```
9. Выполняем SELECT во второй сессии:

```
BEGIN TRANSACTION ISOLATION LEVEL REPEATABLE READ;
select * from persons;

BEGIN
 id | first_name | second_name 
----+------------+-------------
  1 | ivan       | ivanov
  2 | petr       | petrov
  3 | sergey     | sergeev
(3 rows)

```
Новую строку не видим, потому что уровень изоляции `repeatable read` видит только данные, закомиченные до начала транзакции. Он также не видит изменения, закоммиченные во время выполнения транзакции параллельными транзакциями.

10. Завершаем транзакцию в первой сессии и выполняем SELECT во второй сессии:

```
select * from persons;

id | first_name | second_name 
----+------------+-------------
  1 | ivan       | ivanov
  2 | petr       | petrov
  3 | sergey     | sergeev
(3 rows)
```
Новую строку также не видим, так как коммит в первой сессии произошёл после начала текущей транзакции.

11. Завершаем транзакцию во второй сессии и выполняем тот же запрос:

```
rollback;
ROLLBACK

BEGIN TRANSACTION ISOLATION LEVEL REPEATABLE READ;
select * from persons;

id | first_name | second_name 
----+------------+-------------
  1 | ivan       | ivanov
  2 | petr       | petrov
  3 | sergey     | sergeev
  4 | sveta      | svetova
(4 rows)

```
Строку видим, так как изменения были внесены ло начала последней транзакции.
