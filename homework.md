# Домашнее задание

### 1. Создание базы данных (локально):
1.1 Описание нашей БД
```yaml
version: "3.9"
services:
  postgres:
    image: postgres:13.3
    environment:
      POSTGRES_DB: "homework"
      POSTGRES_USER: "postgres"
      POSTGRES_PASSWORD: "postgres"
    ports:
      - "5432:5432"
```
1.2 Запуск контейнера  
`docker-compose up`  

### 2. Создаем схему  
#### Скрипт:  
```sql
create schema homework;
```
#### Результат:  
<img width="290" alt="Screenshot 2024-02-09 at 00 30 29" src="https://github.com/DanilovA93/otus_postgresql/assets/41110190/0d848465-7aee-426c-9311-5f0c449351b6">

### 3. Создаем таблицу и наполняем данными:  
#### Скрипты:  
```sql
create table homework.persons(
    id serial,
    first_name text,
    second_name text
);
insert into homework.persons(first_name, second_name)
values('ivan', 'ivanov'),
      ('petr', 'petrov');
select * from homework.persons;
```
#### Результат:  
| id | first\_name | second\_name |
| :--- | :--- | :--- |
| 1 | ivan | ivanov |
| 2 | petr | petrov |

### 4. Проверяем текущий уровень транзакции:  
#### Скрипт:  
```sql
show transaction isolation level;
```
#### Результат:  
| transaction\_isolation |
| :--- |
| read committed |

### 5. Запускаем две транзакции T1 и T2:  
#### Скрипт:  
```sql
begin;
```  
#### Результат:  
Транзакции запушены  

### 6. В транзакции T1 выполняем вставку и селект:
#### Скрипт:  
```sql
insert into homework.persons(first_name, second_name)
values('sergey', 'sergeev');
select * from homework.persons;
```
#### Результат:  
| id | first\_name | second\_name |
| :--- | :--- | :--- |
| 1 | ivan | ivanov |
| 2 | petr | petrov |
| 4 | sergey | sergeev |

### 7. В транзакции T2 выполняем селект:
#### Скрипт:  
```sql
select * from homework.persons;
```
#### Результат:  
| id | first\_name | second\_name |
| :--- | :--- | :--- |
| 1 | ivan | ivanov |
| 2 | petr | petrov |

### 8. Видите ли вы новую запись и если да то почему?  
Нет, так как вставка в T1 не была закомичена  

### 9. Завершить транзакцию T1:
#### Скрипт:  
```sql
commit;
```

### 10. Сделать селект в транзакции T2:  
#### Скрипт:  
```sql
select * from homework.persons;
```
#### Результат:  
| id | first\_name | second\_name |
| :--- | :--- | :--- |
| 1 | ivan | ivanov |
| 2 | petr | petrov |
| 4 | sergey | sergeev |

### 11. Видите ли вы новую запись и если да то почему?
Да, так как вставка в T1 была закомичена  

### 12. Завершить транзакцию T2:
#### Скрипт:  
```sql
commit;
```

### 13. Начать транзакции T1 и T2 с уровнем изоляции repeatable read:
#### Скрипт:  
```sql
set transaction isolation level repeatable read;
```

### 14. В T1 добавляем новую запись:
#### Скрипт:  
```sql
insert into homework.persons(first_name, second_name) values('sveta', 'svetova');
```

### 15. В T2 делаем селект:
#### Скрипт:  
```sql
select * from homework.persons;
```
#### Результат:  
| id | first\_name | second\_name |
| :--- | :--- | :--- |
| 1 | ivan | ivanov |
| 2 | petr | petrov |
| 4 | sergey | sergeev |

### 16. Видите ли вы новую запись и если да то почему?
Нет, так как вставка в T1 не была закомичена  

### 17. Завершить T1:
#### Скрипт:  
```sql
commit;
```

### 18. Выполнить селект в T2:
#### Скрипт:  
```sql
select * from homework.persons;
```
#### Результат:  
| id | first\_name | second\_name |
| :--- | :--- | :--- |
| 1 | ivan | ivanov |
| 2 | petr | petrov |
| 4 | sergey | sergeev |

### 19. Видите ли вы новую запись и если да то почему?
Нет, так как транзакция T2 не завершена и мы получаем данные из предыдущего снимка БД

### 20. Завершить транзакцию T2
#### Скрипт:  
```sql
commit;
```

### 21. сделать select * from persons во второй сессии
#### Скрипт:  
```sql
select * from homework.persons;
```
#### Результат:  
| id | first\_name | second\_name |
| :--- | :--- | :--- |
| 1 | ivan | ivanov |
| 2 | petr | petrov |
| 4 | sergey | sergeev |
| 5 | sveta | svetova |

### 22. Видите ли вы новую запись и если да то почему?
Да, так как транзакция T1 и T2 завершены
