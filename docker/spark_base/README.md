# Образ Docker  Spark Base
Эта папка содержит Dockerfile, все необходимые файлы конфигурации Hadoop FS, Yarn, Hive и Spark, а также сценарий запуска Docker Entrypoint для контейнера Docker, на котором работает псевдораспределенный одноузловой кластер Hadoop и Spark, включая Jupyter Notebooks (а также Hive и HiveServer2). Вы можете построить его самостоятельно или взять из https://hub.docker.com/r/marcelmittelstaedt/spark_base

## Build/Pull and Run Image:

Build Hadoop Base Image:
```
docker build . -t marcelmittelstaedt/spark_base:latest
```

...or pull Image:
```
docker pull marcelmittelstaedt/spark_base
```

Run Image:
```
docker run -dit --name hadoop \
	-p 8088:8088 -p 9870:9870 -p 9864:9864 -p 10000:10000 \
	-p 8032:8032 -p 8030:8030 -p 8031:8031 -p 9000:9000 \
	-p 8888:8888 --net bigdatanet \
	marcelmittelstaedt/spark_base:latest

```

# Start and Stop Docker Container:
Stop Container:
```
docker stop hadoop
```

Start Container:
```
docker start hadoop
```

# Start and Stop Hadoop, Spark and Jupyter within Container:
Switch to hadoop user:
```
sudo su hadoop
cd
```

Start Hadoop FS and Yarn:
```
start-all.sh
```

Start Spark Shell (with Yarn Master):
```
spark-shell --master yarn
```

Start PySpark Shell (with Yarn Master):
```
pyspark --master yarn
```

Start Jupyter Notebook:
```
jupyter notebook
````

Stop Hadoop FS and Yarn:
```
stop-all.sh
```
