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

