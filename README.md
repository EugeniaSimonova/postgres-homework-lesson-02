# 02 - SQL и реляционные СУБД. Введение в PostgreSQL 

1. Создана ВМ Яндекс.
2. Проверяем и отключаем auto commit:
  > \echo :AUTOCOMMIT<br/>
  > \set AUTOCOMMIT OFF
3. Создаем таблицу в первой сессии
>  create table persons(id serial, first_name text, second_name text); <br/>
>  insert into persons(first_name, second_name) values('ivan', 'ivanov');<br/>
>  insert into persons(first_name, second_name) values('petr', 'petrov');<br/>
>  commit;<br/>
