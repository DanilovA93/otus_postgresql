### 1. создайте новый кластер PostgresSQL 14
#### Скрипт:  
```yml
version: "3.9"
services:
  db:
    image: postgres:14
    container_name: db
    environment:
      POSTGRES_DB: "otus"
      POSTGRES_USER: "postgres"
      POSTGRES_PASSWORD: "postgres"
    volumes:
      - ./var/lib/postgres/data:/var/lib/postgresql/data
    ports:
      - "5432:5432"
```

### 2. зайдите в созданный кластер под пользователем postgres и создайте новую базу данных testdb
```bash
su postgres
psql
```
```sql
create database testdb;
```

### 3. зайдите в созданную базу данных под пользователем postgres
```sql
\c testdb;
```

### 4. создайте новую схему testnm
```sql
create schema testnm;
```

### 5. создайте новую таблицу t1 с одной колонкой c1 типа integer
```sql
create table testnm.t1 (c1 int);
```

### 6. вставьте строку со значением c1=1
```sql
insert into testnm.t1(c1) values(1);
```

### 7. создайте новую роль readonly
```sql
create role readonly;
```

### 8. дайте новой роли право на подключение к базе данных testdb
```sql
grant connect on database testdb to readonly;
```

### 9. дайте новой роли право на использование схемы testnm
```sql
grant usage on schema testnm to readonly;
```

### 10. дайте новой роли право на select для всех таблиц схемы testnm
```sql
grant select on all tables in schema testnm to readonly;
```

### 11. создайте пользователя testread с паролем test123
```sql
create user testread with password 'test123';
```

### 12. дайте роль readonly пользователю testread
```sql
grant readonly to testread;
```

### 13. зайдите под пользователем testread в базу данных testdb
```sql
exit
exit
psql -d testdb -U testread
```

### 14. сделайте select * from t1;
```sql
select * from t1;
```
ошибка: relation "t1" does not exist
причина: не указана схема

### 15. делаем с указанием схемы select * from testnm.t1;
```sql
select * from testnm.t1;
```
результат: 1

### 1. как сделать так чтобы такое больше не повторялось? если нет идей - смотрите шпаргалку  
указывать схему при работе с таблицей  




```sql
\c testdb postgres; 
revoke create on schema public from public; 
revoke all on database testdb from public; 
\c testdb testread; 
```


### 1. сделайте select * from testnm.t1, получилось?
Да  

### 1. теперь попробуйте выполнить команду create table t2(c1 integer); insert into t2 values (2);
### 1. а как так? нам же никто прав на создание таблиц и insert в них под ролью readonly?
### 1. есть идеи как убрать эти права? если нет - смотрите шпаргалку
### 1. если вы справились сами то расскажите что сделали и почему, если смотрели шпаргалку - объясните что сделали и почему выполнив указанные в ней команды
### 1. теперь попробуйте выполнить команду create table t3(c1 integer); insert into t2 values (2);
### 1. расскажите что получилось и почему
