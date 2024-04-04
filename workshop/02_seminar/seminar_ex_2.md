# Семинар_2: HDFS и  Hive QL 

Среда выполнения: виртуальная машина Ubuntu(Cent OS, Debian).

Наиболее часто встречающиеся команды [`HDFS File System`](https://github.com/BosenkoTM/BigDataAnalitic/blob/main/workshop/01_seminar/commands_hdfs.md). 

В [репозитории](https://disk.yandex.ru/d/_bvlfpFNkuGSzw)  представлен образ с `Hadoop 3.3.6+Hive`.

1. Развернуть образ `U20-Hadoop_3_3_6+Docker` в `Oracle VM VirtualBox`. В данном образе установка `Docker` выполнена в полном объеме со всеми дополнениями. Если требуется установить более актуальную версию, выполнить следующую инструкцию:

Запустите следующую команду, чтобы удалить все конфликтующие пакеты:

```bash
for pkg in docker.io docker-doc docker-compose docker-compose-v2 podman-docker containerd runc; do sudo apt-get remove $pkg; done
```
Шаг `1.` Обновить пакеты, которые уже существуют:

```bash
sudo apt update
```

Шаг `2.` Установить пакеты, которые назначат право для “apt” пользоваться пакетами по протоколу HTTPS:

```bash
sudo apt install apt-transport-https ca-certificates curl software-properties-common
```

Шаг `3.` Установить GPG ключ для репозитория Docker в систему:

```bash
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
```

Шаг `4.` Добавить в APT sources репозиторий Docker:

```bash
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu focal stable"
```

Шаг `5.` После чего обновить базу данных пакетами Docker из добавленного репозитория:

```bash
sudo apt update
```

Шаг `6.` Убедиться, что установка будет производиться именно из репозитория Docker:

```bash
apt-cache policy docker-ce
```

Вы увидите данные строчки кода(номер версии для Docker может быть другим):
`docker-ce:
Installed: (none)
Candidate: 5:19.03.9~3-0~ubuntu-focal
Version table:
5:19.03.9~3-0~ubuntu-focal 500
500 https://download.docker.com/linux/ubuntu focal/stable amd64 Packages`

Обратите внимание, что `docker-ce` не установлен, но находится в ожидании на установку в репозитории `Docker` для Ubuntu `20.04`.

Шаг `7.` После устанавливаем `Docker`:

```bash
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

2. Загрузить `Hadoop` с помощью `Hive Image`:

```bash
sudo docker pull marcelmittelstaedt/hive_base:latest
```

3. Запустить контейнер из полученного образа:

```bash
sudo docker run -dit --name hive_base_container -p 8088:8088 -p 9870:9870 -p 9864:9864 marcelmittelstaedt/hive_base:latest
```

4. Просмотреть работающие контейнеры:

```bash
sudo docker ps -a
```
5. Просмотреть журнал контейнера (дождитесь завершения):

```bash
sudo docker logs hive_base_container
```
6. Перейти в контейнер:

```bash
sudo docker exec -it hive_base_container bash
```

7. Переключиться на пользователя `hadoop`:

`root@85ace08fabbe:/# sudo su hadoop
 hadoop@85ace08fabbe:/$ cd`

8. Запустить `DFS` и `YARN`:
 
```bash
start-all.sh
```
    
Чтобы проверить статус всех сервисов `Hadoop`, выполните в терминале команду `jps`:

```bash
jps
 ```
    
 Откройте браузер и войдите в `NameNode` Hadoop, прописываем либо `IP-адрес` или `localhost`, порт `9870`.
- `http://localhost:9870` Пользовательский интерфейс `NameNode` предоставляет полный обзор всего кластера.
- `http://localhost:9864` Используется для доступа к отдельным узлам данных непосредственно из  браузера.
- `http://localhost:8088` Диспетчер ресурсов — это бесценный инструмент, который позволяет отслеживать все запущенные процессы в кластере `Hadoop`.
 Для остановки кластера `Hadoop`, необходимо остановить сервисы `YARN` и `NameNode`.
   
```bash
stop-dfs.sh
stop-yarn.sh
```

9. Проверить доступность `Hive`. Запустить `Hive`:

`hadoop@85ace08fabbe:~$ hive`

## Задание 1. Hive: создание внешних таблиц и работа с ними

В следующих упражнениях используем [Cloudera quickstart VM](https://downloads.cloudera.com/demo_vm/virtualbox/cloudera-quickstart-vm-5.13.0-0-virtualbox.zip).

* Загрузить и разархивировать приложение для VirtualBox.

* Импортировать файл `.ova` в VirtualBox. **сразу не запускайте**, сначало настроить виртуальное окружение.

* Установить для виртуальной машины `cloudera-quickstart-vm-5.13.0-0-virtualbox` диапазон ОЗУ от 4 до 16 ГБ  `Configuration > System`. 

* Запустить виртуальную машину `cloudera-quickstart-vm-5.13.0-0-virtualbox`.

* Чтобы [включить копирование/вставку](https://www.techrepublic.com/article/how-to-enable-copy-and-paste-in-virtualbox/) с ПК, выбрать `Devices > Shared Clipboard > Bidirectional`.


1. Подготовить среду `Hive`.

```bash
wget https://datasets.imdbws.com/title.basics.tsv.gz
```

```bash
wget https://datasets.imdbws.com/title.ratings.tsv.gz
```

```bash
gunzip title.basics.tsv.gz
```

```bash
gunzip title.ratings.tsv.gz
```

```bash
hadoop fs -mkdir /user/hadoop/imdb
```

```bash
hadoop fs -mkdir /user/hadoop/imdb/title_basics
```

```bash
hadoop fs -mkdir /user/hadoop/imdb/title_ratings
```

```bash
hadoop fs -put title.basics.tsv /user/hadoop/imdb/title_basics/title.basics.tsv
```

```bash
hadoop fs -put title.ratings.tsv /user/hadoop/imdb/title_ratings/title.ratings.tsv
```

```sql
CREATE EXTERNAL TABLE IF NOT EXISTS title_ratings(
tconst STRING, 
average_rating DECIMAL(2,1), 
num_votes BIGINT
) COMMENT 'IMDb Ratings'
ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t' STORED AS 
TEXTFILE LOCATION '/user/cloudera/imdb/title_ratings' 
TBLPROPERTIES ('skip.header.line.count'='1');
```

```sql
CREATE EXTERNAL TABLE IF NOT EXISTS title_basics ( 
tconst STRING,
title_type STRING,
primary_title STRING,
original_title STRING,
is_adult DECIMAL(1,0),
start_year DECIMAL(4,0),
end_year STRING,
runtime_minutes INT,
genres STRING
) COMMENT 'IMDb Movies' ROW FORMAT DELIMITED FIELDS TERMINATED BY 
'\t' STORED AS TEXTFILE LOCATION '/user/cloudera/imdb/title_basics'
TBLPROPERTIES ('skip.header.line.count'='1');
```




2. Скачать [датасет](https://datasets.imdbws.com/name.basics.tsv.gz) в контейнер.  
3. Создать каталог в `HDFS` для файла `name.basics.tsv`. 
4. Создайте внешнюю `Hive` таблицу `name_basics`  для `name.basics.tsv`.
5. Используйте `HiveQL`, чтобы ответить на следующие вопросы:
- Сколько фильмов и сериалов находится в наборе данных `IMDB`? 
- Кто самый молодой актер/сценарист/... в наборе данных?
- Создайте `таблицу_результат` (`tconst`, `original_title`, `start_year`, `average_rating`, `num_votes`), которая состоит из:
    - фильм вышел в  `2016` году или позднее;
    - фильм имеет средний рейтинг, равный или превышающий `8,1`.
    - проголосовали более `100 000` раз за фильм.
6. Сколько фильмов находится в `таблице_результате`?

Все результаты выполнения запросов подкрепить скринами из `YARN`, например:
 
  ![My Image](/images/yarn_uns.png)


## Результаты работы представить в виде файла ФИО-02.pdf (выгрузить в `moodle`), в котором отражены следующие результаты:
- постановка задачи;
- скрины хода выполнения работы при выполнении `Задание 1.1` - `Задание 1.6.` ;
- прикрепить также следующие файлы:
  -  файл результатов расчета `таблицу_результат`, 
  -  лог-файл работы `HDFS`;
  - скрины, подтверждающие выпознение работы в `Hadoop`.
  - Все выполненные команды оформить отдельным файлом  в формате `ФИО_группа.txt`.

## Пример выполнения Задание 1. Hive: создание внешних таблиц и работа с ними

`1.` Подготовить среду `Hive`.
`2.` Скачать [датасет](https://datasets.imdbws.com/name.basics.tsv.gz).
```bash
wget https://datasets.imdbws.com/name.basics.tsv.gz
```

```bash
gunzip name.basics.tsv.gz
```
`3-1.` Создаем `HDFS` каталог /user/cloudera/imdb/name_basics для файла `name.basics.tsv`:

```bash
hadoop fs -mkdir /user/cloudera/imdb/name_basics
```
`3-2.` Перемещаем TSV-файл в HDFS каталог

hadoop fs -put name.basics.tsv /user/cloudera/imdb/name_basics

hdfs dfs -ls /user/cloudera/imdb

`4.` Создайте внешнюю `Hive` таблицу `name_basics`  для `name.basics.tsv`:

hive >

```sql
CREATE EXTERNAL TABLE IF NOT EXISTS name_basics( 
nconst STRING,
primary_name STRING,
birth_year INT,
death_year STRING,
primary_profession STRING,
known_for_titles STRING
) COMMENT 'IMDb Actors' ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t' STORED AS TEXTFILE LOCATION '/user/cloudera/imdb/name_basics' 
TBLPROPERTIES ('skip.header.line.count'='1’);
```
Проверка в `HUE`:

```sql
select * from title_basics limit 3;
```

`5.` Используйте `HiveQL`, чтобы ответить на следующие вопросы:
- Сколько фильмов и сериалов находится в наборе данных `IMDB`? 
- Кто самый молодой актер/сценарист/... в наборе данных?
- Создайте `таблицу_результат` (`tconst`, `original_title`, `start_year`, `average_rating`, `num_votes`), которая состоит из:
    - фильм вышел в  `2016` году или позднее;
    - фильм имеет средний рейтинг, равный или превышающий `8,1`.
    - проголосовали более `100 000` раз за фильм.

`a)` Сколько фильмов и сколько сериалов содержится в наборе данных IMDB?

```sql
SELECT m.title_type, count(*)
FROM title_basics m 
GROUP BY m.title_type;
```
`b)` Кто самый молодой актер/сценарист/… в наборе данных?

Просмотреть названия полей для уточнения запросов:

```sql
SELECT * FROM name_basics;
```
Проведем группировку до дате всех пользователей, выявим степень чистоты данных:

```sql
SELECT primary_name as Name, birth_year as dateOfBirth
FROM name_basics n
GROUP BY n.birth_year, n.primary_name;
```
Чистим данные и отвечаем на поставленный вопрос:

```sql
 SELECT primary_name as Name, birth_year as dateOfBirth
FROM name_basics n
WHERE birth_year IS NOT NULL
GROUP BY n.birth_year, n.primary_name
ORDER by n.birth_year DESC;
```
Поиск молодых актеров, один из вариантов:

```sql
SELECT primary_name as Name, birth_year as dateOfBirth
FROM name_basics n
WHERE birth_year IS NOT NULL AND primary_profession LIKE 'actor%'
GROUP BY n.birth_year, n.primary_name
ORDER by n.birth_year DESC;
```
Тоже самое, но с выводом идентификатора `nconst` для поиска по сайту:

```sql
SELECT primary_name as Name, birth_year as dateOfBirth, n.nconst
FROM name_basics n
WHERE birth_year IS NOT NULL AND primary_profession LIKE 'actor%'
GROUP BY n.birth_year, n.primary_name, n.nconst
ORDER by n.birth_year DESC;
```
`c)` Создать список (`m.tconst`, `m.original_title`, `m.start_year`, `r.average_rating`, `r.num_votes`) фильмов, включающий следующие элементы: 
Фильм вышел в `2010` году или позднее.
Фильм имеет средний рейтинг равный или выше `8,1`.
проголосовали за фильм более `100 000` раз.

```sql
SELECT m.tconst, m.original_title, m.start_year, r.average_rating, r.num_votes FROM title_basics m JOIN title_ratings r on (m.tconst = r.tconst)
WHERE r.average_rating >= 8.1 and m.start_year >= 2010 and m.title_type = 'movie' and r.num_votes > 100000
ORDER BY r.average_rating desc, r.num_votes DESC;
```
`d)` Сколько фильмов в списке с)

```sql
SELECT count(*)
FROM title_basics m JOIN title_ratings r on (m.tconst = r.tconst)
WHERE r.average_rating >= 8.1 and m.start_year >= 2010 and m.title_type = 'movie' and r.num_votes > 100000;
```
`e)` Требуется узнать, какие годы были великими для кино. Создайте список с одной строкой в год и соответствующим количеством фильмов, которые:
имеют средний рейтинг выше 8 проголосовали за фильм более 100 000 раз в порядке убывания по количеству фильмов.

```sql
SELECT m.start_year, count(*)
FROM title_basics m JOIN title_ratings r on (m.tconst = r.tconst)
WHERE r.average_rating > 8 AND m.title_type = 'movie' 
AND r.num_votes > 100000
GROUP BY m.start_year
ORDER BY count(*) DESC;
```
`f)` Итак, 1995 год кажется действительно хорошим годом для кино, было выпущено 8 действительно хороших фильмов, но какие?

```sql
SELECT
		m.tconst, m.original_title, m.start_year, r.average_rating, 
		r.num_votes
	  FROM title_basics m JOIN title_ratings r ON (m.tconst = r.tconst) 
         WHERE
		r.average_rating > 8 AND m.title_type = 'movie' 
		AND r.num_votes > 100000 AND m.start_year = 1995
	  ORDER BY r.average_rating DESC;
```











 
