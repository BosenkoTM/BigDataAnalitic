# Образ Docker для Hadoop и Hive Base
Эта папка содержит файл Dockerfile, все необходимые файлы конфигурации Hadoop FS, Yarn и Hive, а также сценарий запуска Docker Entrypoint для контейнера Docker, на котором работает псевдораспределенный одноузловой кластер Hadoop, включая Hive. Вы можете построить его самостоятельно или взять из https://hub.docker.com/r/marcelmittelstaedt/hive_base

## Сборка/извлечение и запуск образа:

Создание базового образа Hadoop:
```
docker build . -t marcelmittelstaedt/hive_base:latest
```

...или извлечь образ:
```
docker pull marcelmittelstaedt/hive_base
```

Запуск образа:
```
docker run -dit --name hive_base_container -p 8088:8088 -p 9870:9870 -p 9864:9864 marcelmittelstaedt/hive_base:latest
```

# SЗапуск и остановка Docker-контейнера:
Остановить контейнер:
```
docker stop hive_base_container
```

Запустить контейнер:
```
docker start hive_base_container
```

# Запуск и остановка Hadoop и Hive внутри контейнера:
Переключиться на пользователя Hadoop:
```
sudo su hadoop
cd
```

Запустить Hadoop FS и Yarn.:
```
start-all.sh
```

Запустить интерфейс командной строки Hive:
```
hive
```

Остановить Hadoop FS и Yarn:
```
stop-all.sh
```
