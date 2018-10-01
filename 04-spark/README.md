# Universidad EAFIT
# Curso ST0263 Tópicos Especiales en Telemática, 2018-2
# Profesor: Edwin Montoya M. – emontoya@eafit.edu.co

# LAB SPARK

* Se puede programar en python, scala o java.

* ejemplos en python:

1. De forma interactiva via 'pyspark'

// ya trae preconfigurado las variables sc y spark

    $ pyspark
    >>> files = sc.textFile("hdfs:///datasets/gutenberg-small/*.txt")
    >>> counts = text_file.flatMap(lambda line: line.split(" ")).map(lambda word: (word, 1)).reduceByKey(lambda a, b: a + b)
    >>> counts.saveAsTextFile("hdfs:///tmp/wcout1")

    * asi salva counts un archivo por rdd.
    * si quiere que se consolide en un solo archivo de salida:

    $ pyspark
    >>> files = sc.textFile("hdfs:///datasets/gutenberg-small/*.txt")
    >>> counts = text_file.flatMap(lambda line: line.split(" ")).map(lambda word: (word, 1)).reduceByKey(lambda a, b: a + b)
    >>> counts.coalesce(1).saveAsTextFile("hdfs:///tmp/wcout2")

2. Como un archivo python: [wc-spark.py](wc-spark.py)

``` Python
    from pyspark.sql import SparkSession

    spark = SparkSession.builder.appName("WordCount").getOrCreate()

    sc = spark.sparkContext

    text_file = sc.textFile("hdfs:///datasets/gutenberg-small/*.txt")
    counts = text_file.flatMap(lambda line: line.split(" ")) \
                 .map(lambda word: (word, 1)) \
                 .reduceByKey(lambda a, b: a + b)
    counts.saveAsTextFile("hdfs:///tmp/spout1")
```    

* correrlo:

    $ spark-submit --master yarn --deploy-mode cluster wc-spark.py

3. Desde Zeppelin Nodebook:

Entre desde un browser a: http://10.131.137.X:9995

![login](zeppelin-login.png)

Cree un Notebook:

![crear Notebook](zeppelin-create1.png)

![crear Notebook](zeppelin-create2.png)

Wordcount en python:

```python
    %spark2.pyspark
    text_file = sc.textFile("hdfs:///datasets/gutenberg-small/*.txt")
    counts = text_file.flatMap(lambda line: line.split(" ")).map(lambda word: (word, 1)).reduceByKey(lambda a, b: a + b)
    counts.saveAsTextFile("hdfs:///tmp/spout1")
```

wordcount en spark.sql

    %spark2.sql
    SHOW tables

    %spark2.sql
    SHOW database

    %spark2.sql    
    USE <user_vpn>

    %spark2.sql   
    CREATE EXTERNAL TABLE <user_vpn>.docs2 (line STRING) stored as textfile location '/datasets/gutenberg-small/*.txt'

    %spark2.sql
    SELECT word, count(1) AS count FROM (SELECT explode(split(line,' ')) AS word FROM docs2) w GROUP BY word ORDER BY word
