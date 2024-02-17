# Домашнее задание

### 1. Создание директории для постоянного хранения данных из контейнера:
<img width="1150" alt="Screenshot 2024-02-17 at 15 51 45" src="https://github.com/DanilovA93/otus_postgresql/assets/41110190/d6e87c93-6f1b-4455-b23f-328e702dc482">

### 2. Создание docker network
```unix
docker network create --driver bridge db-net
```

### 3. Создание контейнера базы данных (локально):
3.1 Описание нашей БД
```yaml
version: "3.9"
services:
  db:
    image: postgres:15.6
    container_name: db
    networks:
      - db-net
    environment:
      POSTGRES_DB: "otus"
      POSTGRES_USER: "postgres"
      POSTGRES_PASSWORD: "postgres"
    volumes:
      - ./var/lib/postgres:/var/lib/postgresql
    ports:
      - "5433:5432"
networks:
  db-net:
    external: true
```
3.2 Запуск контейнера  
`docker-compose up`  

### 4. Создание клиента для подключения к базе данных (локально):
4.1 Описание клиента
```yaml
version: "3.9"
services:
  client:
    image: postgres:15.6-alpine
    container_name: client
    networks:
      - db-net
    environment:
      POSTGRES_DB: "otus"
      POSTGRES_USER: "postgres"
      POSTGRES_PASSWORD: "postgres"
networks:
  db-net:
    external: true
```
4.2 Запуск контейнера  
`docker-compose up`  

### 5. Подключаемся к клиенту
```unix
docker exec -it 1a10175db171 /bin/bash
```
### 6. Подключаемся к контейнеру бд из контейнера клиента и создание таблицы с ее наполнением:
6.1 Подключение к бд внешнего контейнера  
<img width="551" alt="Screenshot 2024-02-17 at 15 59 34" src="https://github.com/DanilovA93/otus_postgresql/assets/41110190/61d72225-b51b-4139-8b05-4b9cb0c552d5">  
6.2 Создание схемы  
<img width="285" alt="Screenshot 2024-02-17 at 16 03 05" src="https://github.com/DanilovA93/otus_postgresql/assets/41110190/ef763ab8-855f-4a07-a2fb-b505db0d38f6">  
6.3 Создание таблицы  
<img width="541" alt="Screenshot 2024-02-17 at 16 03 11" src="https://github.com/DanilovA93/otus_postgresql/assets/41110190/dae0998c-5c55-420c-a226-8eb650613ce0">  
6.4 Наполнение таблицы  
<img width="667" alt="Screenshot 2024-02-17 at 16 04 14" src="https://github.com/DanilovA93/otus_postgresql/assets/41110190/3d1e750c-5ab0-4774-8887-0d389e77d9b9">  

### 6. Подключаемся к базе данных с хоста и смотрим данные:
<img width="804" alt="Screenshot 2024-02-17 at 16 09 01" src="https://github.com/DanilovA93/otus_postgresql/assets/41110190/c0b8459f-d0ae-4a6e-bb30-7f0244970268">  
<img width="294" alt="Screenshot 2024-02-17 at 16 10 18" src="https://github.com/DanilovA93/otus_postgresql/assets/41110190/a7115f9b-ff81-4fa5-b23d-6775b6331916">  

### 7. Удаляем контейнер с сервером:
флаг -f используем для удаление работающего контейнера  
<img width="1033" alt="Screenshot 2024-02-17 at 16 23 50" src="https://github.com/DanilovA93/otus_postgresql/assets/41110190/b703d918-e932-45e5-b2d2-4e50bd3a8f4b">

### 8. Заново создаем контейнер с бд:
```unix
docker-compose up
```
<img width="1179" alt="Screenshot 2024-02-17 at 16 26 15" src="https://github.com/DanilovA93/otus_postgresql/assets/41110190/66542fca-5f68-4f11-81fd-35909441432b">  

### 9. Подключаемся к контейнеру с бд из контейнера клиента и проверяем наличие данных:
<img width="462" alt="Screenshot 2024-02-17 at 16 30 17" src="https://github.com/DanilovA93/otus_postgresql/assets/41110190/8b8d3693-9e07-4a4e-834b-fe820295d231">  
ДАННЫХ НЕТ!

### 10. Изменяем параметр volumes в контейнере БД:
Было:  
```yaml
    volumes:
      - ./var/lib/postgres:/var/lib/postgresql
```
Стало:  
```yaml
    volumes:
      - ./var/lib/postgres/data:/var/lib/postgresql/data
```
### 10. Повторяем весь процесс и проверяем наличие данных
<img width="348" alt="Screenshot 2024-02-17 at 16 44 24" src="https://github.com/DanilovA93/otus_postgresql/assets/41110190/065d1aa2-9cca-480c-94f2-45f38c95406e">  
Данные сохраняются после уничтожения контейнера
