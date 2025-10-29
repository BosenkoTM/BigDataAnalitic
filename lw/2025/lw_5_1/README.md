# Лабораторная работа 5.1. Анализ данных землетрясений с использованием Hadoop

[![Docker](https://img.shields.io/badge/Docker-Ready-blue)]()
[![Hadoop](https://img.shields.io/badge/Hadoop-3.3.4-green)]()
[![Python](https://img.shields.io/badge/Python-3.8+-blue)]()

## 📋 Постановка задачи

**Цель.** Получить практические навыки развертывания одноузлового кластера Hadoop, освоить базовые операции с распределенной файловой системой HDFS, выполнить загрузку и обработку данных, а также проанализировать и визуализировать результаты.

**Аналитическая задача.** Определить тип землетрясения с максимальной средней магнитудой из датасета исторических данных (вариант 30).

**Источник данных.** https://www.kaggle.com/datasets/usgs/earthquake-database

---

## 🏗 Архитектура системы

```
┌──────────────────────────────────────────────────────────┐
│              Docker Container (Ubuntu 20.04)             │
├──────────────────────────────────────────────────────────┤
│                                                          │
│  ┌──────────────┐    ┌──────────────┐    ┌──────────┐    │
│  │  NameNode    │    │   DataNode   │    │Resource  │    │
│  │  (HDFS)      │◄──►│   (HDFS)     │    │ Manager  │    │
│  └──────────────┘    └──────────────┘    └──────────┘    │
│         │                                             │  │
│         │                                             │  │
│  ┌──────────────┐    ┌──────────────┐    ┌──────────┐    │
│  │ Secondary    │    │   DataNode   │    │   Node   │    │
│  │   NameNode   │    │   (HDFS)     │    │ Manager  │    │
│  └──────────────┘    └──────────────┘    └──────────┘    │
│                                                          │
│  ┌──────────────────────────────────────────────────┐    │
│  │           HDFS File System                       │    │
│  │    /user/hadoop/input/database.csv               │    │
│  └──────────────────────────────────────────────────┘    │
│                                                          │
│  ┌──────────────────────────────────────────────────┐    │
│  │         Python / PySpark Analysis                │    │
│  │    • Pandas для быстрого анализа                 │    │
│  │    • PySpark для больших данных                  │    │
│  │    • Jupyter для визуализации                    │    │
│  └──────────────────────────────────────────────────┘    │
└─────────────────────────────────────────────────────────┘
                │
                │ HTTP
                ▼
        http://localhost:9870 (HDFS UI)
        http://localhost:8088 (YARN UI)
```

---

## 🔧 Технологический стек

| Технология | Версия | Назначение |
|-----------|--------|-----------|
| **Hadoop** | 3.3.4 | Распределенная обработка данных |
| **HDFS** | 3.3.4 | Распределенная файловая система |
| **YARN** | 3.3.4 | Управление ресурсами кластера |
| **Python** | 3.8+ | Язык программирования |
| **Pandas** | 2.0+ | Анализ и обработка данных |
| **PySpark** | 3.4+ | Обработка больших данных |
| **Jupyter** | latest | Интерактивный анализ |
| **Matplotlib** | 3.7+ | Визуализация данных |
| **Docker** | latest | Контейнеризация окружения |
| **Ubuntu** | 20.04 | Базовая ОС контейнера |

---

## 🚀 Запуск проекта

### Шаг 1. Клонирование и подготовка

```bash
# Клонировать репозиторий
git clone <repository_url>
cd lw_5_1

# Убедиться, что файлы на месте
ls -la hadoop/
ls -la scripts/
ls -la notebooks/
```

**Проверка:** Должны увидеть 6 файлов в директории `hadoop/`:
- `workers`
- `core-site.xml`
- `hdfs-site.xml`
- `yarn-site.xml`
- `mapred-site.xml`
- `log4j.properties`

### Шаг 2. Запуск Docker контейнера

```bash
# Запустить контейнер
# docker compose up --build # if new data rewrite database.csv
docker compose up -d

# Просмотреть логи (подождите 60-90 секунд)
docker compose logs -f hadoop

# Ожидаемый вывод в конце:
# === Статус сервисов ===
# [должны быть: NameNode, DataNode, SecondaryNameNode, ResourceManager, NodeManager]
```

**Проверка:** В логах не должно быть ошибок "JAVA_HOME is not set" или "Connection refused".

### Шаг 3. Подключение к контейнеру

```bash
# Открыть терминал внутри контейнера
docker compose exec hadoop bash

# Проверить, что вы внутри контейнера
hostname  # должен показать: hadoop
```

### Шаг 4. Проверка компонентов Hadoop

```bash
# Проверить статус всех процессов
jps

# Должны увидеть:
# - NameNode
# - DataNode
# - SecondaryNameNode
# - ResourceManager
# - NodeManager
# - Jps
```

**Проверка результата.** Убедитесь, что все 5 процессов запущены (NameNode, DataNode, SecondaryNameNode, ResourceManager, NodeManager).

---

## 📁 Работа с HDFS

### Шаг 1. Создание директорий

```bash
# Создать директории для входных и выходных данных
hdfs dfs -mkdir -p /user/hadoop/input
hdfs dfs -mkdir -p /user/hadoop/output

# Проверить созданные директории
hdfs dfs -ls /user/hadoop/
```

**Проверка.** Должны увидеть директории `input` и `output`.

### Шаг 2. Загрузка данных

```bash
# Загрузить dataset в HDFS
hdfs dfs -put /opt/data/database.csv /user/hadoop/input/database.csv

# Проверить загрузку
hdfs dfs -ls -h /user/hadoop/input/

# Просмотреть размер файла
hdfs dfs -du -h /user/hadoop/input/
```

**Проверка.** Файл `database.csv` должен быть в HDFS. Вы увидите его размер (~5-10 MB).

### Шаг 3. Просмотр данных в HDFS

```bash
# Просмотреть первые строки файла из HDFS
hdfs dfs -cat /user/hadoop/input/database.csv | head -20

# Проверить статистику HDFS
hdfs dfsadmin -report
```

**Проверка.** Должны увидеть заголовки CSV: `Date,Time,Latitude,Longitude,Type,Depth,Depth Error...`

### Шаг 4. Веб-интерфейсы (открыть в браузере)

```bash
# Открыть в браузере (на хосте, не в контейнере):
```

- **HDFS NameNode UI:** http://localhost:9870
  - Навигация. Browse the file system → `/user/hadoop/input/` → `database.csv`
  
- **YARN ResourceManager UI:** http://localhost:8088

**Проверка.** В браузере должны открыться веб-интерфейсы Hadoop. В HDFS UI можно увидеть загруженный файл.

---

## 🔍 Анализ данных

### Вариант 1. Pandas (быстрый анализ)

```bash
cd /opt/scripts

# Запустить анализ
python3 analyze_pandas.py

# Проверить результат
cat ../results/magnitude_by_type.csv
head -10 ../results/magnitude_by_type.csv
```

**Проверка результата:** 
- Должен вывестись тип землетрясения с максимальной средней магнитудой
- Файл `results/magnitude_by_type.csv` должен быть создан
- Просмотрите первые строки CSV файла

### Вариант 2. PySpark (для больших данных)

```bash
cd /opt/scripts

# Запустить анализ через Spark
python3 analyze_spark.py

# Проверить результаты в HDFS
hdfs dfs -ls /user/hadoop/output
hdfs dfs -cat /user/hadoop/output/magnitude_by_type/part-00000 | head -20
```

**Проверка результата.** Результаты должны быть в HDFS в директории `/user/hadoop/output/`.

### Вариант 3. Jupyter Notebook (визуализация)

```bash
# Запустить Jupyter без токена (удобно для разработки)
cd /opt
bash scripts/start_jupyter.sh

# Открыть браузер: http://localhost:8888
# (доступ без токена - открывается сразу)

# Открыть notebook: notebooks/earthquake_analysis.ipynb
# Выполнить все ячейки (Run All)
```

**Проверка:** 
- Jupyter должен открыться в браузере по адресу http://localhost:8888
- Выполните команду `hdfs dfs -ls /user/hadoop/input` в первой ячейке - должен показать загруженный файл
- Выполнить все ячейки в notebook (Run → Run All)
- Посмотреть графики и результаты анализа в директории `results/`

---

## 📊 Ожидаемый результат

После выполнения анализа вы получите:

1. **Тип землетрясения с максимальной средней магнитудой**
2. **Таблицу результатов** - `results/magnitude_by_type.csv` с распределением по типам
3. **Визуализации**:
   - График средней магнитуды по типам (топ-10)
   - Распределение количества землетрясений vs магнитуда
   - Box plot распределения магнитуд
4. **Выводы и интерпретация** результатов

### Пример проверки результата

```bash
# Просмотреть результаты
cat results/magnitude_by_type.csv

# Примерный вывод:
# Type,Mean_Magnitude,Count
# Nuclear Explosion,5.85,7
# Explosion,5.32,124
# ...
```

**Проверка.** Просмотрите таблицу - первый тип в списке должен иметь максимальную среднюю магнитуду.

---

## 📝 Структура отчета

Отчет должен быть загружен в Git-репозиторий и включать:

### 1. README.md (этот файл)
- Описание проекта
- Инструкции по запуску
- Результаты анализа

### 2. Исходный код
```
notebooks/
├── earthquake_analysis.ipynb  # Jupyter notebook с анализом
scripts/
├── analyze_pandas.py         # Pandas анализ
└── analyze_spark.py         # PySpark анализ
```

### 3. Результаты анализа
```
results/
├── magnitude_by_type.csv        # Таблица результатов
├── magnitude_by_type.png        # График
└── report.txt                   # Текстовый отчет
```

### 4. Конфигурация
```
hadoop/                          # Конфигурация Hadoop
Dockerfile                       # Docker образ
docker-compose.yml              # Docker Compose конфигурация
requirements.txt                 # Python зависимости
```

### 5. Скриншоты веб-интерфейсов
- HDFS NameNode UI с загруженными файлами
- YARN ResourceManager UI
- Jupyter с визуализациями

---

## 📈 Критерии оценки (10 баллов)

| Критерий | Баллы | Описание |
|----------|-------|----------|
| **Настройка Hadoop** | 2 | Корректное развертывание, все сервисы запущены |
| **Загрузка в HDFS** | 1 | Данные загружены и видны в веб-интерфейсе |
| **Решение через Hadoop** | 2 | Анализ выполнен с использованием HDFS |
| **Решение через Spark** | 2 | Анализ выполнен через PySpark |
| **Визуализация** | 2 | Качественные графики и визуализации |
| **Отчет и оформление** | 1 | Полный отчет в Git-репозитории |

**Требования к отчету:**
- Титульный лист с вариантом задания
- Постановка задачи
- Архитектура системы
- Технологический стек
- Пошаговое решение с проверками
- Результаты анализа с графиками
- Заключение
- Список литературы

---

## 🔄 Управление проектом

### Пересборка проекта

```bash
# Остановить контейнер
docker compose down

# Пересобрать образ
docker compose build --no-cache

# Запустить заново
docker compose up -d

# Проверить статус
docker compose logs -f hadoop
```

### Запуск проекта

```bash
# Если контейнер уже собран
docker compose up -d

# Подключиться к контейнеру
docker compose exec hadoop bash

# Hadoop запускается автоматически при старте контейнера
# Проверить статус
jps
```

### Очистка проекта

```bash
# Остановить и удалить контейнер
docker compose down

# Удалить контейнер + volumes (все данные HDFS)
docker compose down -v

# Удалить образ
docker rmi hadoop-earthquake:latest

# Полная очистка (все Docker ресурсы)
docker system prune -a
```

---

## 🌐 Веб-интерфейсы

После запуска контейнера доступны:

- **HDFS NameNode:** http://localhost:9870
- **YARN:** http://localhost:8088
- **Jupyter:** http://localhost:8888 (без токена)

---

## ❓ FAQ

### Q: Hadoop не запускается
**A:** Пересоберите контейнер:
```bash
docker compose down
docker compose build --no-cache
docker compose up -d
```

### Q: Веб-интерфейсы не открываются
**A:** Конфигурация использует `0.0.0.0` для доступа извне. Пересоберите контейнер (см. выше).

### Q: DataNode не запускается
**A:** Проверьте файл `hadoop/workers`:
```bash
cat hadoop/workers  # должно быть: localhost
sed -i 's/\r$//' hadoop/workers  # исправить окончания строк
```

### Q: Jupyter не открывается
**A:** Запустите Jupyter внутри контейнера:
```bash
docker compose exec hadoop bash
cd /opt
bash scripts/start_jupyter.sh
# Откройте http://localhost:8888 в браузере
```

---

## 📄 Лицензия

Учебный проект для лабораторной работы.

---

## 🎯 Итоговая проверка

Перед завершением работы убедитесь, что выполнены все шаги:

### ✅ Чеклист выполнения

- [ ] Docker контейнер запущен и работает
- [ ] Все сервисы Hadoop запущены (проверить через `jps`)
- [ ] Данные загружены в HDFS (`/user/hadoop/input/database.csv`)
- [ ] Веб-интерфейсы открываются в браузере
- [ ] Анализ выполнен через Pandas или Spark
- [ ] Результаты сохранены в `results/`
- [ ] Jupyter notebook выполнен, созданы визуализации
- [ ] Git-репозиторий создан с полным отчетом
- [ ] Все скриншоты веб-интерфейсов добавлены
- [ ] Отчет соответствует критериям оценки

### 📦 Создание Git-репозитория

```bash
# Инициализировать репозиторий
git init

# Добавить все файлы
git add .

# Создать первый коммит
git commit -m "Initial commit: Hadoop Earthquake Analysis Project"

# Добавить удаленный репозиторий (GitHub/GitLab)
git remote add origin <your-repository-url>

# Загрузить изменения
git push -u origin main
```

### 🔍 Проверка результатов

```bash
# Проверить наличие результатов
ls -lh results/

# Просмотреть результаты
cat results/magnitude_by_type.csv
head -5 results/report.txt

# Проверить веб-интерфейсы
echo "Откройте в браузере:"
echo "  HDFS: http://localhost:9870"
echo "  YARN: http://localhost:8088"
echo "  Jupyter: http://localhost:8888"
```

---

**Проект готов к использованию! 🎉**


