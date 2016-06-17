# 概述

Spark 是基于内存计算的大数据分布式计算框架.
Spark 基于内存计算, 提高了在大数据环境下数据处理的实时性, 同时保证了高容错性和高可伸缩性,
允许用户将 Spark 部署在大量廉价硬件之上, 形成集群.
-   分布式计算
-   内存计算
-   容错
-   多计算范式

BDAS 生态系统

-   Mesos
-   HDFS
-   Tachyon
-   Spark
-   Spark Streaming
-   Spark SQL
-   GraphX
-   MLlib

Spark 计算模型
-   输入与构造 RDD
-   转换 Transformation
-   输出 Action

Spark 计算模型 = 数据结构 RDD + 算法

两类 RDD 函数支撑
-   Transformation
    -   map(func)
    -   filter(func)
    -   reduceByKey(func)
-   Action
    -   reduce(func)
    -   collect()
    -   count()
    -   first()
    -   take(n)

RDD

-   partition
-   compute
-   dependencies
-   partitioner
-   preferredLocations

Spark下的大量小文件的处理
在编写Spark程序的时候，应尽量避免产生大量的小文件，因为这将会给map -> shuffle的过程增加大量的元数据（例如：某一个key／value对存储在哪个文件上），
Spark的通信框架采用Akka，用来传输元数据，它的设计并不是用来传输大量数据的。大量的小文件会导致shuffle的失败。如果无法避免大量小文件的情况出现，可以采用函数
Loaders.combineTextFile(sc, input, partitionSizeInMB)。然而即使是这样，也并不能完全解决问题，这也取决于集群的性能和集群当时使用情况，
partitionSizeInMB 过大会导致，executor资源不足，lost，partitionSizeInMB 过小又会反复大量小文件的问题，所以仍需要不断调试partitionSizeInMB 和executor－number参数才能最终跑出结果。
下面附上应用于大量小文件的WordCount源代码：
package com.dianping.poi.nlp.job

import org.apache.hadoop.conf.Configuration
import org.apache.hadoop.io.LongWritable
import org.apache.hadoop.io.Text
import org.apache.hadoop.mapreduce.lib.input.CombineTextInputFormat
import org.apache.spark.{SparkConf, SparkContext}

/**
 * Created by DanDan on 16/6/8.
 */
object WordCount {
  def main(args: Array[String]) {
    val input = args(0)
    val output = args(1)
    val partitionSizeInMB = args(2).toInt

    println("================================================================")
    println("Input: " + input)
    println("Output: " + output)
    println("Partitions: " + partitionSizeInMB)
    println("================================================================")

    val conf = new SparkConf().setAppName("WordCount")
    val sc = new SparkContext(conf)


    val data = Loaders.combineTextFile(sc, input, partitionSizeInMB)

    data.map((_, 1)).reduceByKey(_ + _)
      .filter(_._2 > 4)
      .saveAsTextFile(output)
  }
}
执行命令：
nohup /usr/local/hadoop/spark-1.4.1/bin/spark-submit --class com.dianping.poi.nlp.job.WordCount --master yarn --executor-memory 7g --driver-memory 16g --num-executors 40 --conf "spark.executor.extraJavaOptions=-XX:MaxPermSize=4096M"  --conf spark.driver.maxResultSize=3g --conf spark.kryoserializer.buffer.max=2047m --driver-java-options -XX:MaxPermSize=4096m /data/star-learning/wangqiudan/origin-avatar-nlp/target/avatar-nlp-qa-1.0-SNAPSHOT-jar-with-dependencies.jar dependency10 word10 16 >> log.txt &
