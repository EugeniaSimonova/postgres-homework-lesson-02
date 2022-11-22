# 02 - SQL и реляционные СУБД. Введение в PostgreSQL 

1. Создана ВМ Яндекс.
2. Проверяем и отключаем auto commit:
  ```
  \echo :AUTOCOMMIT<br/>
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
5. Добавляем новую записть в первой сессии:

```
insert into persons(first_name, second_name) values('sergey', 'sergeev');

INSERT 0 1
```
6.Делаем `select * from persons` во второй сессии:

```

```
