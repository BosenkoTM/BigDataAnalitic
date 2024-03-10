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

1. Подготовить среду `Hive`.
2. Скачать [датасет](https://datasets.imdbws.com/name.basics.tsv.gz) в контейнер.  
3. Создать каталог в `HDFS` /user/hadoop/imdb/name_basics/ для файла `name.basics.tsv`. 
4. Создайте внешнюю `Hive` таблицу `name_basics`  для `name.basics.tsv`.
5. Используйте `HiveQL`, чтобы ответить на следующие вопросы:
- Сколько фильмов и сериалов находится в наборе данных `IMDB`? 
- Кто самый молодой актер/сценарист/... в наборе данных?
- Создайте `таблицу_результат` (`tconst`, `original_title`, `start_year`, `average_rating`, `num_votes`), которая состоит из:
    - фильм вышел в  `2016` году или позднее;
    - фильм имеет средний рейтинг, равный или превышающий `8,1`.
    - проголосовали более `100 000` раз за фильм.
6. Сколько фильмов находится в `таблице_результате`?

## Результаты работы представить в виде файла ФИО-02.pdf (выгрузить в `moodle`), в котором отражены следующие результаты:
- постановка задачи;
- скрины хода выполнения работы при выполнении `Задание 1.1` - `Задание 1.6.` ;
- прикрепить также следующие файлы:
  -  файл результатов расчета `таблицу_результат`, 
  -  лог-файл работы `HDFS`;
  - скрины, подтверждающие выпознение работы в `Hadoop`.
  - Все выполненные команды оформить отдельным файлом  в формате `ФИО_группа.txt`.
