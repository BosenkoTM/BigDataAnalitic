# Лабораторная работа 4-1. Сравнение подходов хранения больших данных

## Цель работы
Сравнить производительность и эффективность различных подходов к хранению и обработке больших данных на примере реляционной базы данных PostgreSQL и документо-ориентированной базы данных MongoDB.

## Оборудование и программное обеспечение
- Компьютер с операционной системой Ubuntu
- Docker и Docker Compose
- PostgreSQL 16
- MongoDB 4.4 (стабильная версия)
- Python 3.x
- Библиотеки: psycopg2, pymongo, pandas, matplotlib, jupyter

## Постановка задачи

### Теоретическая часть
В современном мире объемы данных растут экспоненциально, что приводит к необходимости использования эффективных методов их хранения и обработки. Существует два основных подхода к хранению больших данных:

1. **Реляционные базы данных** (например, PostgreSQL) - используют структурированные таблицы с фиксированной схемой.
2. **NoSQL базы данных** (например, MongoDB) - используют документную модель с гибкой схемой.

Каждый из этих подходов имеет свои преимущества и недостатки, которые мы рассмотрим в ходе выполнения лабораторной работы.

### Задачи, которые требуется решить:
1. Настройка окружения с использованием Docker Compose.
2. Создание и наполнение баз данных тестовыми данными.
3. Реализация рекомендательной системы в обеих СУБД.
4. Сравнение производительности запросов.
5. Анализ результатов и выводы.

## Установка и настройка

### Шаг 1. Подготовка окружения в Ubuntu

1. **Создание рабочего каталога:**
```bash
mkdir -p ~/big_data_lab
cd ~/big_data_lab
```

2. **Установка Docker и Docker Compose:**
```bash
# Обновление пакетов
sudo apt update

# Установка Docker
sudo apt install -y docker.io docker-compose

# Добавление пользователя в группу docker
sudo usermod -aG docker $USER

# Перезагрузка сессии (или выполните: newgrp docker)
```

3. **Клонирование проекта**
```bash
# Если используете git
git clone <repository_url> .
# Или скопируйте файлы в каталог
```

### Шаг 2. Запуск сервисов

1. **Запуск всех сервисов:**
```bash
sudo docker-compose up -d
```

2. **Проверка статуса сервисов:**
```bash
sudo docker-compose ps
```

3. **Просмотр логов (при необходимости):**
```bash
sudo docker-compose logs -f
```

### Шаг 3. Настройка баз данных

#### PostgreSQL
1. **Подключение к PostgreSQL:**
```bash
sudo docker exec -it postgresql psql -U postgres -d studpg
```

2. **Создание пользователя student:**
```sql
CREATE USER student WITH PASSWORD 'Stud2024!!!';
GRANT ALL PRIVILEGES ON DATABASE studpg TO student;
GRANT ALL PRIVILEGES ON ALL TABLES IN SCHEMA public TO student;
\q
```

#### MongoDB
1. **Подключение к MongoDB:**
```bash
sudo docker exec -it mongodb mongosh -u mongouser -p mongopass
```

2. **Создание базы данных studmongo:**
```javascript
use studmongo
db.createCollection("users")
db.createCollection("products")
db.createCollection("views")
```

## Таблица доступа к сервисам

| Сервис | URL/Хост | Порт | Логин | Пароль | Описание |
|--------|----------|------|-------|--------|----------|
| **MongoDB** | localhost | 27017 | mongouser | mongopass | Основная база данных MongoDB |
| **Mongo Express** | http://localhost | 8081 | adminuser | adminpasswd | Веб-интерфейс для MongoDB |
| **PostgreSQL** | localhost | 5432 | postgres | changeme | Основная база данных PostgreSQL |
| **pgAdmin** | http://localhost | 5050 | pgadmin4@pgadmin.org | admin | Веб-интерфейс для PostgreSQL |
| **Jupyter** | http://localhost | 8888 | - | jupyter123 | Jupyter Notebook для анализа |

### Дополнительные учетные данные для PostgreSQL:
- **Пользователь student:** student / Stud2024!!!
- **База данных:** studpg

## Пример выполнения индивидуального задания

### Задание №30. Рекомендательная система

**Цель.** Создать систему рекомендаций товаров на основе поведения пользователей.

#### Для PostgreSQL
1. Создать таблицы:
   - `users` (id, name, email, age)
   - `products` (id, name, category, price)
   - `views` (user_id, product_id, timestamp)

2. Найти товары, которые просматривал пользователь X, а также другие пользователи, смотревшие те же товары.

#### Для MongoDB
1. Создать коллекции:
   - `users` (с массивом просмотренных товаров)
   - `products` (информация о товарах)

2. Написать сложный агрегационный запрос для поиска "похожих" пользователей.

#### Анализ в Jupyter Notebook
Сравнить производительность и сложность реализации простого запроса для коллаборативной фильтрации ("users who viewed this also viewed").

## Подробные шаги решения для задания №30

### Шаг 1. Подготовка данных

1. **Генерация тестовых данных (10,000 записей для обычных данных, 100,000 для больших данных):**
```python
import random
import string
from datetime import datetime, timedelta

def generate_users(n):
    users = []
    for i in range(n):
        name = f"User_{i}"
        email = f"user{i}@example.com"
        age = random.randint(18, 65)
        users.append((i, name, email, age))
    return users

def generate_products(n):
    products = []
    categories = ['Electronics', 'Clothing', 'Books', 'Home', 'Sports']
    for i in range(n):
        name = f"Product_{i}"
        category = random.choice(categories)
        price = round(random.uniform(10, 1000), 2)
        products.append((i, name, category, price))
    return products

def generate_views(users_count, products_count, n):
    views = []
    for _ in range(n):
        user_id = random.randint(0, users_count-1)
        product_id = random.randint(0, products_count-1)
        timestamp = datetime.now() - timedelta(days=random.randint(0, 365))
        views.append((user_id, product_id, timestamp))
    return views
```

### Шаг 2. Реализация в PostgreSQL

1. **Создание таблиц:**
```sql
-- Создание таблицы пользователей
CREATE TABLE users (
    id SERIAL PRIMARY KEY,
    name VARCHAR(100),
    email VARCHAR(100),
    age INTEGER
);

-- Создание таблицы продуктов
CREATE TABLE products (
    id SERIAL PRIMARY KEY,
    name VARCHAR(100),
    category VARCHAR(50),
    price DECIMAL(10,2)
);

-- Создание таблицы просмотров
CREATE TABLE views (
    id SERIAL PRIMARY KEY,
    user_id INTEGER REFERENCES users(id),
    product_id INTEGER REFERENCES products(id),
    timestamp TIMESTAMP
);

-- Создание индексов для оптимизации
CREATE INDEX idx_views_user_id ON views(user_id);
CREATE INDEX idx_views_product_id ON views(product_id);
CREATE INDEX idx_views_timestamp ON views(timestamp);
```

2. **Запрос для поиска похожих пользователей:**
```sql
-- Найти товары, которые просматривал пользователь X
WITH user_products AS (
    SELECT DISTINCT product_id 
    FROM views 
    WHERE user_id = 1
),
similar_users AS (
    SELECT v.user_id, COUNT(*) as common_products
    FROM views v
    INNER JOIN user_products up ON v.product_id = up.product_id
    WHERE v.user_id != 1
    GROUP BY v.user_id
    HAVING COUNT(*) > 0
    ORDER BY common_products DESC
    LIMIT 10
)
SELECT u.name, su.common_products
FROM similar_users su
JOIN users u ON su.user_id = u.id;
```

### Шаг 3. Реализация в MongoDB

1. **Структура документов:**
```javascript
// Коллекция users
{
    "_id": ObjectId("..."),
    "name": "User_1",
    "email": "user1@example.com",
    "age": 25,
    "viewed_products": [
        {"product_id": 1, "timestamp": ISODate("2023-01-01")},
        {"product_id": 5, "timestamp": ISODate("2023-01-02")}
    ]
}

// Коллекция products
{
    "_id": ObjectId("..."),
    "name": "Product_1",
    "category": "Electronics",
    "price": 299.99
}
```

2. **Агрегационный запрос для поиска похожих пользователей:**
```javascript
db.users.aggregate([
    // Найти пользователя X
    { $match: { "_id": ObjectId("user_x_id") } },
    
    // Получить его просмотренные товары
    { $unwind: "$viewed_products" },
    { $project: { "product_id": "$viewed_products.product_id" } },
    
    // Найти других пользователей, которые смотрели те же товары
    {
        $lookup: {
            from: "users",
            let: { "target_product": "$product_id" },
            pipeline: [
                { $unwind: "$viewed_products" },
                { $match: { 
                    $expr: { 
                        $and: [
                            { $eq: ["$viewed_products.product_id", "$$target_product"] },
                            { $ne: ["$_id", ObjectId("user_x_id")] }
                        ]
                    }
                }},
                { $project: { "user_id": "$_id", "product_id": "$viewed_products.product_id" } }
            ],
            as: "similar_users"
        }
    },
    
    // Группировка и подсчет
    { $unwind: "$similar_users" },
    {
        $group: {
            "_id": "$similar_users.user_id",
            "common_products": { $sum: 1 }
        }
    },
    
    // Сортировка и ограничение
    { $sort: { "common_products": -1 } },
    { $limit: 10 }
])
```

### Шаг 4. Сравнение производительности

1. **Измерение времени выполнения запросов**
```python
import time
import psycopg2
from pymongo import MongoClient

def measure_postgres_performance():
    start_time = time.time()
    # Выполнение SQL запроса
    # ... код запроса ...
    end_time = time.time()
    return end_time - start_time

def measure_mongodb_performance():
    start_time = time.time()
    # Выполнение MongoDB агрегации
    # ... код агрегации ...
    end_time = time.time()
    return end_time - start_time
```

2. **Визуализация результатов**
```python
import matplotlib.pyplot as plt

# Сравнение времени выполнения
databases = ['PostgreSQL', 'MongoDB']
times = [postgres_time, mongodb_time]

plt.figure(figsize=(10, 6))
plt.bar(databases, times, color=['blue', 'green'])
plt.title('Сравнение времени выполнения запросов рекомендательной системы')
plt.xlabel('База данных')
plt.ylabel('Время выполнения (секунды)')
plt.show()
```

## Проверка работоспособности

### 1. Проверка MongoDB
```bash
# Подключение к MongoDB
sudo docker exec -it mongodb mongosh -u mongouser -p mongopass

# Проверка баз данных
show dbs

# Проверка коллекций
use studmongo
show collections
```

### 2. Проверка PostgreSQL
```bash
# Подключение к PostgreSQL
sudo docker exec -it postgresql psql -U postgres -d studpg

# Проверка таблиц
\dt

# Проверка пользователей
\du
```

### 3. Проверка веб-интерфейсов
- **Mongo Express:** http://localhost:8081
- **pgAdmin:** http://localhost:5050
- **Jupyter:** http://localhost:8888

### 4. Проверка Jupyter Notebook
```bash
# Запуск Jupyter
sudo docker exec -it jupyter jupyter notebook list
```

## Остановка сервисов

```bash
# Остановка всех сервисов
sudo docker-compose down

# Остановка с удалением volumes (ОСТОРОЖНО: удалит все данные!)
sudo docker-compose down -v
```

## Заключение

В ходе выполнения лабораторной работы мы:

1. Настроили окружение с MongoDB 4.4 и PostgreSQL 16.
2. Реализовали рекомендательную систему в обеих СУБД.
3. Сравнили производительность и сложность реализации.
4. Проанализировали преимущества и недостатки каждого подхода.

**Основные выводы:**
- PostgreSQL лучше подходит для сложных аналитических запросов с множественными JOIN.
- MongoDB более гибкая для работы с неструктурированными данными.
- Выбор СУБД зависит от конкретных требований проекта.

## Дополнительные ресурсы

- [Документация MongoDB](https://docs.mongodb.com/)
- [Документация PostgreSQL](https://www.postgresql.org/docs/)
- [Docker Compose документация](https://docs.docker.com/compose/)
- [Jupyter Notebook документация](https://jupyter-notebook.readthedocs.io/)

