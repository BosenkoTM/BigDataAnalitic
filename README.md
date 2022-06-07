# Lecturer
Timur Bosenko M. (bosenkotm@mgpu.ru)

Здесь вы можете найти все материалы по лекции **`Аналитика больших данных: подходы и инструменты`**. 

Эти лекции дадут вам краткое введение в то, что называется «большими данными». Мы быстро обновим основы баз данных, моделей данных и обработки данных, сравним их с распределенным миром больших данных. После этого мы углубимся в основы распределенных хранилищ данных и обработки данных, а также в связанные концепции и проблемы надежности, масштабируемости, репликации, секционирования, пакетной и потоковой обработки. Позже рассмотрим наиболее распространенное программное обеспечение и фреймворки (в основном это экосистема Hadoop). В конце, когда вы знаете основные концепции и умеете настраивать и работать с распределенными средами и огромными наборами данных, будет краткое введение в науку о данных.

В конце каждого урока будут практические упражнения, которые мы начнем вместе и которые должны быть завершены к следующей встрече. Так что будьте внимательны, и если вы не успеваете, не стесняйтесь задавать вопросы в конце каждого урока.

## Текущая успеваемость

 [ЦТ-201мз, АБД 2022](https://docs.google.com/spreadsheets/d/1ndnkqm8tmnvmygQrt_wuMUwJiVAsIgEqtLOMsLNlZMM/edit?usp=sharing)

## Big Data Analytics: Approaches and Tools

- `lecture 01` Intro:
    -  [Introduction to Big Data](lectures/1-BigData_Intro.pdf)

- `lecture 02` File Systems:
    - [Hadoop Distributed File System (HDFS)](lectures/2-BigData_HDFS.pdf)
-  Practice: 
    -  [Introduction to HDFS](https://github.com/BosenkoTM/BigDataAnalitic_Practice/tree/main/exercises/winter_semester_2021-2022/01_hadoop).
    -  `Решение` задания [Introduction to HDFS](https://github.com/BosenkoTM/BigDataAnalitic_Practice/blob/main/solutions/winter_semester_2021-2022/01_hadoop/Exercise_1.pdf).

- `lecture 03` Cluster Managers:
    - [Yet Another Resource Negotiator (YARN)](lectures/3-BigData_YARN.pdf)
-  Practice: 
    -  [Introduction to YARN + Hive](https://github.com/BosenkoTM/BigDataAnalitic_Practice/tree/main/exercises/winter_semester_2021-2022/02_hive).
    -  `Решение` задания [Introduction to YARN + Hive](https://github.com/BosenkoTM/BigDataAnalitic_Practice/blob/main/solutions/winter_semester_2021-2022/02_hive/Exercise_2.pdf).
    -  [Introduction to HiveQL](https://github.com/BosenkoTM/BigDataAnalitic_Practice/tree/main/exercises/winter_semester_2021-2022/03_hive-ql_partitioning_hive-server).
    -  `Решение` задания [Introduction to HiveQL](https://github.com/BosenkoTM/BigDataAnalitic_Practice/tree/main/solutions/winter_semester_2021-2022/03_hive-ql_partitioning_hive-server).

- `lecture 04` Batch processing:
    - [MapReduce Framework](lectures/4-BigData_MapReduce.pdf)
    - [Introduction to Spark](BigData_Spark.pdf)
    - [User-Defined Functions (UDF) in PySpark](BigData_PySpark_UDF.pdf)
 
- `lecture 05` Coordination:
    - [Introduction to Zookeeper](BigData_Zookeeper.pdf)
   
- `lecture 06` Streaming:
    - [Apache Storm](BigData_Storm.pdf)
    - [Spark Streaming](BigData_Spark_Streaming.pdf)
    - [Spark Structured Streaming]()
- Practice:
    - [Introduction to Spark_PySpark](https://github.com/BosenkoTM/BigDataAnalitic_Practice/tree/main/exercises/winter_semester_2021-2022/04_spark_pyspark_jupyter)
    -  `Решение` задания [Introduction to Spark_PySpark](https://github.com/BosenkoTM/BigDataAnalitic_Practice/tree/main/solutions/winter_semester_2021-2022/04_spark_pyspark_jupyter).
- `lecture 07` Graphs:
    - [Apache Giraph](BigData_Giraph.pdf)
    - [Spark GraphX](BigData_GraphX.pdf)
    
- `lecture 08` Containers:
    - [Introduction to Docker](BigData_Docker.pdf)

## Теоретические вопросы

1.	Основные парадигмы теории Больших данных. Правило Мура. Правила Амдала. Виды распределенных систем в контексте больших данных.
2.	Распределенная файловая система Hadoop (`HDFS`).
3.	Менеджеры кластера: (`YARN`).
4.	Инструменты работы с большими данными `Hive` и  `HiveQL`.
5.	Инструменты работы с большими данными при пакетной обработке данных: Фреймворк `MapReduce`. 
6.	Инструменты работы с большими данными `Spark` и `PySpark`.
7.	Координатор: основные подходы при работе в `Zookeeper`.
8.	Инструменты работы с большими данными при потоковой передаче данных: `Apache Storm`, `Spark Streaming`, `Spark Structured Streaming`.
9.	Инструменты работы с большими данными на Графах: `Apache Giraph`, `Spark GraphX`.
10.	Контейнеры: основные подходы при работе  в `Docker`.

## self-study

- `Семинар 1`. [Основные команды в Ubuntu](https://github.com/BosenkoTM/BigDataAnalitic_Practice/blob/main/common/docs/basic_shell_commands.md)

## ТЕСТ 1. Apache Spark.  Дата проведения 25.06.2022, 13.00 - 14.30

[ССЫЛКА ДЛЯ ВХОДА](https://docs.google.com/forms/d/e/1FAIpQLSdD_Hl-WwPK69VGKKf0tw1vF3AgMKYQRR3w9RofcIFKlJM4YA/viewform?usp=sf_link)

## ТЕСТ 2. Зачетное тестирование. Дата проведения  29.06.2022 18.00 - 19.30
[ССЫЛКА ДЛЯ ВХОДА](https://docs.google.com/forms/d/e/1FAIpQLSdxADzRpGOzLrVBPHwUQMt6sWIPO63nLwYt0KolAWKq3-xREQ/viewform?usp=sf_link)

## Зачетное практическое задание по курсу  Big Data Analytics: Approaches and Tools

### `Вариант 1`

Использование `kaggle.com` Набора Данных **`Hubway`** Для Расчета Ключевых Показателей Эффективности Совместного Использования Велосипедов. [Условие задания](https://github.com/BosenkoTM/BigDataWork/blob/main/variant_1_exam_calculate/examp_1.pdf)

### `Вариант 2`

Использование данных **`NYC_Taxi`** для Расчета Ключевых Показателей Эффективности. [Условие задания](https://github.com/BosenkoTM/BigDataWork/blob/main/variant_2_exam_calculate/examp_2.pdf)

### `Вариант 3`

Настройка и работа в **`Hadoop`**, **`HDFS`** и **`Yarn`**. [Условие задания](https://github.com/BosenkoTM/BigDataWork/blob/main/variant_3_exam_calculate/examp_3.pdf)

### `Вариант 4`

Установка, настройка и работа в **`Hive`**. [Условие задания](https://github.com/BosenkoTM/BigDataWork/tree/main/variant_4_exam_calculate)


## Additional topics

- Linux and Python:
    - [Introduction to Linux Kernel](common/SysProg_Intro.pdf)
    - [Virtual File System](common/SysProg_VFS.pdf)
    - [Network Protocols](common/SysProg_NetworkProtocols.pdf)

## Summary table of literature sources
Разделы | 👨‍🏫 Курсы | 📚 Книги | 📊 Данные | 🙋‍♂️ Посты | ✊ Софт
--- | --- | --- | --- | --- | ---
Big Data | [Курсы по Big Data](books/courses_big_data.md) | [Книги по Big Data](books/software_big_data.md#книги-по-big-data) | + | [Источники, посвященные Big Data](books/social_data_science.md) | [Программы и библиотеки для работы с большими данными](/books/software_big_data.md#программы-и-библиотеки-для-bigdata)

