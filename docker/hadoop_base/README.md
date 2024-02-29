# Hadoop Base Docker Image
Эта папка содержит файл Dockerfile, все необходимые файлы конфигурации Hadoop FS и Yarn, а также сценарий запуска Docker Entrypoint для контейнера Docker, на котором работает псевдораспределенный одноузловой кластер Hadoop. Вы можете построить его самостоятельно или взять из https://hub.docker.com/r/marcelmittelstaedt/hadoop_base

## Сборка/извлечение и запуск образа:

Сборка базового образа Hadoop:
```
docker build . -t marcelmittelstaedt/hadoop_base:latest
```

...или извлечь весь образ:
```
docker pull marcelmittelstaedt/hadoop_base
```

Запуск образа:
```
docker run -dit --name hadoop_base_container -p 8088:8088 -p 9870:9870 marcelmittelstaedt/hadoop_base:latest
```

# Запуск и остановка Docker-контейнера:
Остановить контейнер:
```
docker stop hadoop_base_container
```

Запустить контейнер:
```
docker start hadoop_base_container
```

# Запуск и остановка Hadoop внутри контейнера:
Переключиться на пользователя Hadoop:
```
sudo su hadoop
cd
```

Запустите Hadoop FS и Yarn.:
```
start-all.sh
```

Stop Hadoop FS and Yarn:
```
stop-all.sh
```
