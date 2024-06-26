# Семинар_1: Hadoop & MapReduce (local)
Среда выполнения: виртуальная машина Ubuntu(Cent OS, Debian).

Наиболее часто встречающиеся команды [`HDFS File System`](https://github.com/BosenkoTM/BigDataAnalitic/blob/main/workshop/01_seminar/commands_hdfs.md). 

В [репозитории](https://disk.yandex.ru/d/gRMDe32McGortA)  представлен образ с `Hadoop 3.3.6`. Важно! Обновлен образ(08.03.2024) до  версии `U20-Hadoop_3_3_6 clear`, в котором выполнять все задания.

Запуск `Node` в терминале:
  
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
    
## Задание 1. Работа в CLI оболочке ​​ОС Linux.

> Note: Примечание. Большинство команд оболочки имеют следующий шаблон вызова:
> `<COMMAND> [<OPTIONS>] <ARGS>` где:
> - `<COMMAND>` это команда, например `pwd`, `ls`, `cd`
> - `[<OPTIONS>]` необязательный список опций, которые изменят поведение команды, например, `-l` опция при вызове `ls`
> - `<ARGS>` это список аргументов/параметров, которые будут прочитаны командой для выполнения ее функций, например, `mkdir foo`, где `foo` одно значение передается в качестве аргумента `mkdir` команде

- `Задание 1.1.` Для чего предназначена команда `pwd`?

    ```bash
    $ pwd
    ```

- `Задание 1.2.` Для чего предназначена команда `ls`?

    ```bash
    $ ls
    $ ls -a
    $ ls -a -l
    $ ls -a -l -h
    ```
- `Задание 1.3.` Для чего предназначены команды `mkdir` и `rmdir`?

    ```bash
    $ mkdir foo bar
    $ ls -l
    $ mkdir baz
    $ ls -l
    $ rmdir bar
    $ ls -l
    $ mkdir -p try/test
    $ ls -l -R
    ```
    > - Подсказка: многие команды оболочки представляют собой аббревиатуру названий действий, которые 
    >   пользователь может выполнять при работе на своем компьютере..

- `Задание 1.4.` Для чего предназначена команда `cd`?

    ```bash
    $ pwd
    $ cd foo
    $ cd ..
    $ pwd
    $ cd try/test
    $ pwd
    $ cd ../..
    $ cd try/test
    $ pwd
    $ cd ~
    $ pwd
    ```
    > - Question #2: Что происходит, когда вы используете `~` в качестве аргумента при вызове
    >  `cd`?

- `Задание 1.5.` Напишите какой-нибудь текст в программе CLI с использованием `nano`

    ```bash
    $ nano a_file.txt
    # Напишите какой-нибудь текст в программе CLI
    # Вы можете сохранить свою работу, нажав кнопки CTRL-O.
    # Выйти из nano можно, нажав кнопки CTRL-X.
    ```

    > Примечание: «nano» — это программа текстового редактора, работающая в CLI. Довольно часто у нас нет > доступа к графическому интерфейсу пользователя (GUI) при работе с компьютером/сервером, который находится удаленно.
    > Поэтому очень важно ознакомиться с командами и программами на основе CLI в оболочке.

- `Задание 1.6.` Какой результат выполнения команды `cat`? 

    ```bash
    $ cat a_file.txt
    ```

- `Задание 1.7.` Для чего предназначена команда `cp`?

    ```bash
    $ cp a_file.txt b_file.txt
    $ ls -l
    $ cp a_file.txt foo/a_file.txt
    $ ls -l -R
    ```

- `Задание 1.8.` Что произошло с `a_file.txt` файлом?

    ```bash
    $ ls -l
    $ rm a_file.txt
    $ ls -l
    ```

Теперь у вас есть базовые знания по использованию основных команд, которые можно вызывать при взаимодействии с компьютером под управлением Linux. В этом случае главный узел на самом деле является сервером на базе Linux, и все упражнения в последующих разделах потребуют от вас вызова команд в оболочке.


## Задание 2. Управление файлами в HDFS

В отличие от традиционной модели программирования, в которой мы пытаемся перенести данные в программу , модель программирования MapReduce делает противоположное, то есть переносит программу в данные . Сами данные также распределяются по нескольким узлам кластера. Поэтому вам необходимо знать, как получить данные из кластера и поместить их в кластер с помощью команд, взаимодействующих с распределенной файловой системой Hadoop (HDFS). Как только вы сможете управлять своими данными, вы можете запустить программу MapReduce для выполнения вычислений над распределенными данными.

Чтобы управлять файлами данных в кластере Hadoop, вам необходимо знать несколько основных команд, предоставляемых базовой установкой Hadoop на главном узле. Команды аналогичны основным командам оболочки в ОС Linux. Полный список команд можно прочитать в следующей ссылке: 

https://hadoop.apache.org/docs/r3.3.6/hadoop-project-dist/hadoop-common/FileSystemShell.html.

Ниже приведено несколько примеров основных команд, которые вы часто будете использовать при взаимодействии с HDFS.

- `Задание 2.1.` Получить список файлов в HDFS:

    ```bash
    $ hadoop fs -ls
    $ hadoop fs -ls /foo/bar
    ```
    > Объяснение: В первом примере будет получен список файлов в корневом (самом верхнем) каталоге HDFS. 
    > Во втором примере будет получен список файлов в каталоге `/bar`, который находится в каталоге `/foo` > в корне HDFS.


- `Задание 2.2.` Скопировать файл с локального компьютера (сервера/главного узла) в HDFS:

    ```bash
    $ hadoop fs -copyFromLocal a_file.txt
    # Alternative
    $ hadoop fs -put a_file.txt
    ```

- `Задание 2.3.` Скопировать файл в HDFS:

    ```bash
    $ hadoop fs -cp a_file.txt b_file.txt
    ```

     > - Вопрос №1: Что делает `hadoop fs -cp <ARG_1> <ARG_2>`?
     > - Подсказка: попробуйте вызвать Hadoop fs -ls после выполнения приведенной выше команды.

- `Задание 2.4.` Скопировать файл из HDFS на локальный компьютер. Можно ли скопировать файл из HDFS и присвоить ему другое имя на локальном компьютере за одно выполнение `hadoop fs`? Если да, то как?

    ```bash
    $ hadoop fs -copyToLocal b_file.txt
    # Alternative
    $ hadoop fs -get b_file.txt
    ```

- `Задание 2.5.` Отобразить содержимое файла в HDFS:

    ```bash
    $ hadoop fs -cat a_file.txt
    ```

- `Задание 2.6.` Удалить файл в HDFS:

    ```bash
    $ hadoop fs -rm a_file.txt
    ```

## Задание 3. Тестирование работы MapReduce

Базовая установка Hadoop предоставляет несколько примеров программ MapReduce. Список примеров программ можно просмотреть, выполнив следующие команды:

```bash
# Измените текущий активный каталог на свой домашний каталог
$ cd ~
# Скопировать коллекцию программ в текущий каталог
$ cp /opt/hadoop-3.3.6/share/hadoop/mapreduce/hadoop-mapreduce-examples-3.3.6.jar .
# Запустить программу, чтобы просмотреть список доступных примеров программ MapReduce.
$ hadoop jar hadoop-mapreduce-examples-3.3.6.jar
```

запустить программу MapReduce под названием `wordcount`. Вы можете увидеть параметры и аргументы, необходимые программе, выполнив следующую команду: 

```bash
$ hadoop jar hadoop-mapreduce-examples-3.3.6.jar wordcount
```

Программе `wordcount` требуется список аргументов, где первыми аргументами являются входные файлы, а последним аргументом — путь к выходному каталогу. Входные файлы должны быть помещены в HDFS, прежде чем их можно будет обработать программой. Поэтому вам необходимо скопировать все входные файлы в HDFS перед запуском программы MapReduce.

После того как вы скопировали все входные файлы, вы можете запустить `wordcount` программу MapReduce, выполнив следующую команду:


```bash
$ hadoop jar hadoop-mapreduce-examples-3.3.6.jar wordcount input/input1.txt input/input2.txt output
```

> Объяснение: Программа `wordcount` будет использовать  `input1.txt` и `input2.txt` в `input` каталоге 
> HDFS в качестве файлов входных данных. Результаты вычислений будут записаны в новую папку под названием > `output`  HDFS.
 Cкопировать результаты из HDFS в свой каталог на локальном компьютере и просмотреть содержимое каждого файла результатов.


Congratulations! You just run your first MapReduce program on Hadoop! Now try to
copy the results from HDFS to your own directory in local machine and see the
content of each result files.

Клонировать репозиторий git (чтобы получить  данные):

```bash
$ git clone https://github.com/BosenkoTM/BigDataAnalitic.git
```

- `Задание 3.1.` Просмотреть список доступных примеров программ `MapReduce` в Hadoop.
- `Задание 3.2.` Скопировать файл примера(repo/01_hadoop/data/Faust_en.txt) из Git Repo в HDFS.
- `Задание 3.3.` Запустите MapReduce Jar по умолчанию(hadoop/share/hadoop/mapreduce/hadoop-mapreduce-examples-3.3.6.jar) для расчета количества слов в текстовом файле.
- `Задание 3.4.` Скопируйте результат задания MapReduce обратно в локальную файловую систему Ubuntu.
- `Задание 3.5.` Запустите Jar MapReduce по умолчанию (hadoop/share/hadoop/mapreduce/hadoop-mapreduce-examples-3.3.6.jar), чтобы получить количество вхождений строки «Faust» в текстовом файле.
- `Задание 3.6.` Скопируйте результат задания MapReduce обратно в локальную файловую систему Ubuntu. 

## Пример выполнения Задание 3. Тестирование работы MapReduce (файл War_and_Peace.txt)

**Задание 3.1.** Клонировать каталог с данными для анализа:

```bash
hadoop@mgpu-VirtualBox:~$ git clone https://github.com/BosenkoTM/RepoData.git 
```
- Проверить наличие искомого файла, в примере `War_and_Peace.txt`:

```bash
hadoop@mgpu-VirtualBox:~/RepoData/BigDataAnalitic/01_hadoop/data$ ls
```
Результат работы команды:

`Faust_de.txt  Faust_en.txt  Faust_ru.txt  War_and_Peace.txt`

**Задание 3.2.** Скопировать файл из локальной папки в `HDFS`, предварительно создать каталог.

```bash
hadoop fs -mkdir /mgpu/mapreduce
```
```bash
hadoop fs -copyFromLocal -f RepoData/BigDataAnalitic/01_hadoop/data/War_and_Peace.txt /mgpu/mapreduce
```
- Проверка наличия файла в `HDFS`:

```bash
hadoop fs -ls /mgpu/mapreduce
```

Результат работы команды:

` Found 1 items
-rw-r--r--   1 hadoop supergroup    2058499 2024-03-08 14:45 /mgpu/mapreduce/War_and_Peace.txt`

**Задание 3.3.** Запустить `MapReduce` для расчета количества слов в текстовом файле.  

  `Jar-файл` по умолчанию расположен в каталоге: 

`$HADOOP_HOME/share/hadoop/mapreduce/hadoop-mapreduce-examples-3.3.6.jar`

- Проверить путь к системному `Jar-файлу`, в котором реализован алгоритм подсчета слов в тексте:

```bash
sudo find / -name "hadoop-mapreduce-examples-3.3.6.jar"
```
Результат работы команды:

`/home/hadoop/hadoop/share/hadoop/mapreduce/hadoop-mapreduce-examples-3.3.6.jar`

- Выполнить подсчет слов в тексте `War_and_Peace.txt`:
```bash
hadoop jar $HADOOP_HOME/share/hadoop/mapreduce/hadoop-mapreduce-examples-3.3.6.jar wordcount /mgpu/mapreduce/War_and_Peace.txt  /mgpu/mapreduce/War_and_Peace_Output
```
- Проверить доступность Ноды в Диспетчере ресурсов `Yarn`, для этого перейти по адресу в браузере по ссылке `http://localhost:8088/cluster/nodes`. В меню слева в закладке `Cluster/Nodes` просмотреть общую информацию о кластере (у нас псевдокластер):

![My Image](/images/map_reduce_01.jpg)
  
- Проверить результат рвботы задания по расчету количества слов в тексте. Перейти в меню слева в закладку `FINISHED`  `http://mgpu-virtualbox:8088/cluster/apps/FINISHED` и проверить статус `FinalStatus`:

 ![My Image](/images/map_reduce_02.jpg)

- Перейти по ссылке в браузере (http://localhost:9870/explorer.html#/mgpu/mapreduce) в каталог `HDFS` для проверки полученного результата:

  ![My Image](/images/map_reduce_03.jpg)
 
Видим, что задача выполнялась `4` раза, конечный каталог `War_and_Peace_Output4`.

- Зайти в каталог `War_and_Peace_Output4`, скачать файл `part-r-00000`.

  ![My Image](/images/map_reduce_04.jpg)
  
- Провести частотный анализ слов файла `part-r-00000` с использованием внешних программных средств визуализации.
- На гистограмме указать `5` самых часто встречающихся слов, как правило — это существительное или глагол в единственном числе, учесть, что требуется сначала избавиться от стоп-слов, предлогов и союзов.

**Индивидуальное задание**. Создать `jar-файл` [`wordcount v2.0`](https://hadoop.apache.org/docs/current/hadoop-mapreduce-client/hadoop-mapreduce-client-core/MapReduceTutorial.html#Example:_WordCount_v2.0), скомпилировать его и реализовать подсчет слов в тексте. 

**Задание 3.5** Запустить Jar MapReduce по умолчанию (hadoop/share/hadoop/mapreduce/hadoop-mapreduce-examples-3.3.6.jar), чтобы получить количество вхождений строки «war» в текстовом файле `War_and_Peace.txt`.

- Скопировать образец файла из репозитория `GIT` в каталог пользователя `HDFS`:

- Создать каталог:

```bash
hadoop fs -mkdir /mgpu/mapreduce2
```

- Перенести файл:

```bash
git clone https://github.com/BosenkoTM/RepoData.git
```

```bash
hadoop fs -put RepoData/BigDataAnalitic/01_hadoop/data/War_and_Peace.txt /mgpu/mapreduce2
```

- Произвести расчет встречаемости слова `war`:
```bash
hadoop jar $HADOOP_HOME/share/hadoop/mapreduce/hadoop-mapreduce-examples-3.3.6.jar grep /mgpu/mapreduce2/War_and_Peace.txt /mgpu/mapreduce2/War_and_Peace_Count_Output 'war'
```
- Проверить результат рвботы задания по расчету вхождений строки «war» в тексте. Перейти в меню слева в закладку `FINISHED`  `http://mgpu-virtualbox:8088/cluster/apps/FINISHED` и проверить статус `FinalStatus`:

 ![My Image](/images/map_reduce_05.jpg)

- Перейти по ссылке в браузере (http://localhost:9870/explorer.html#/mgpu/mapreduce2/War_and_Peace_Count_Output) в каталог `HDFS` для проверки полученного результата:

  ![My Image](/images/map_reduce_06.jpg)

## Результаты работы представить в виде файла ФИО-01.pdf (выгрузить в `moodle`), в котором отражены следующие результаты:
- постановка задачи;
- скрины хода выполнения работы при выполнении `Задание 1.` - `Задание 3.` ;
- прикрепить также следующие файлы:
  -  файл результатов расчета количества слов в тексте, 
  -  файл результатов расчета встречаемости заданного слова в тексте(получить задание у преподавателя);
  -  лог-файл работы `HDFS`;
  - скрины, подтверждающие выполнение работы в `Hadoop`.
  - частотный анализ текста(в Python+DataLens), выполненный на основе полученного промежуточного результата в `Hadoop`.
  - Все выполненные команды оформить отдельным файлом  в формате `ФИО_группа.txt`.
