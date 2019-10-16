### Reference Links
1. Spark lastest version doc: http://spark.apache.org/docs/latest/. For historical versions, find [here](http://spark.apache.org/documentation.html).  
2. Github repo of Apache Spark (*ASF, Apache Software Foundation*): https://github.com/apache/spark . [Contribute it with your PR](https://github.com/apache/spark/blob/master/CONTRIBUTING.md)!
3. Submit a bug at https://issues.apache.org/jira/projects/SPARK/summary .
4. 《Learning Spark》

### What is Spark ?

> Spark is a `___` ? Currently (these definitions are changable in developer community and updated overtime) we said ...

- Spark is a unified analytics engine for large-scale data processing. 
	> 关于 *是什么的定义(what-is)* 时常随着开源项目本身的发展 PMC Member 们会进行讨论、调整(下一个Major Releasae), 简单来说, Spark是一个分布式计算框架(引擎), 很多开发者常喜欢用它和MapReduce做比较.
- It provides high-level APIs in Scala, Java, Python, and R, and an optimized engine that supports general computation graphs(**DAG, Directed Acyclic Graph**) for data analysis. 
	> 高阶 API 又可以划分为 RDD / Dataset (Dataframe, Spark>=2.0之后 Dataframe API 和 Dataset 进行了合并) 2大类, 后者是社区参考著名的 Python 包 Pandas 的 API 设计风格(更利于分析师使用) 二次封装 RDD API 实现 
- It also supports a rich set of higher-level tools including Spark SQL for SQL and DataFrames, MLlib for machine learning, GraphX for graph processing, and Structured Streaming for stream processing.
	> 除了可以直接使用上面提到的 RDD/Dataset API 开发 Spark Applicaiton 之外. Spark 还提供针对特定场景的内嵌库(built-in library).
	1. Structured Dataset Query & SQL -- Spark SQL 在构建现代化数仓中相当流行
	2. Structured Streaming -- Spark Streaming 由于 Spark2.0 后 Dataset API 的统一, 使得Spark Streaming可以受利于Spark SQL
	3. GraphX 图计算
	4. MLlib 机器学习

### Install Spark

Download spark via https://spark.apache.org/downloads.html .

Enusre you have JDK(>=1.8) installed yet first (`java -version`). Since Spark uses Hadoop’s client libraries for HDFS and YARN, you could : 
- download a pr-built spark(aka binary version) with a specified hadoop version, e.g. Chose a package type "Pre-built for Apache Hadoop 2.7 and later"
- download a pr-built spark only (choose "Pre-built with user-provided Apache Hadoop") and setup spark CLASSPATH like below.
	```
	# download any hadoop binary version (e.g. 2.8.2) and setup Pseudo-Distributed 单机伪分布

	# setup spark CLASSPATH
	vi conf/spark-env.sh
	export SPARK_DIST_CLASSPATH=$(hadoop classpath)	
	```
- download spark source code (or clone from github), then build it with specified hadoop dependency

**For version sepecific, YOU SHOULD KNOW** :
1. Spark runs on Java 8, Python 2.7+/3.4+ and R 3.1+. For the Scala API, Spark 2.4.4 uses Scala 2.12. You will need to use a compatible Scala version (2.12.x).
2. Support for Java 7, Python 2.6 and old Hadoop versions before 2.6.5 were removed as of Spark 2.2.0.
3. Support for Scala 2.10 was removed as of 2.3.0.
4. Support for Scala 2.11 is deprecated as of Spark 2.4.1 and will be removed in Spark 3.0.

### Run a Example Locally 

More details refer to https://spark.apache.org/docs/latest/submitting-applications.html .

``` bash
$ cd spark-2.4.4-bin-hadoop2.7

$ ./bin/spark-submit --class org.apache.spark.examples.SparkPi \
                     --master local[2] \
                     examples/jars/spark-examples_2.11-2.4.4.jar 10

# --class          指定 spark app 的入口类, 必须包含 main 方法
# --master         指定 master URL, local[2] 表示 本地执行 & 2个worker线程
# examples/xxx.jar 指定 jar包 路径
# 10               入口类 main 方法接受的参数 String[] args

# 这是一个计算圆周率的实例，下面是部分STDOUT打印出来的日志
...
19/10/09 15:26:06 INFO SparkContext: Submitted application: Spark Pi
...
19/10/09 15:26:07 INFO Utils: Successfully started service 'sparkDriver' on port 63772.
19/10/09 15:26:07 INFO SparkEnv: Registering MapOutputTracker
19/10/09 15:26:07 INFO SparkEnv: Registering BlockManagerMaster
...
19/10/09 15:26:07 INFO BlockManager: Using org.apache.spark.storage.RandomBlockReplicationPolicy for block replication policy
...
19/10/09 15:26:08 INFO SparkContext: Starting job: reduce at SparkPi.scala:38
19/10/09 15:26:08 INFO DAGScheduler: Got job 0 (reduce at SparkPi.scala:38) with 2 output partitions
19/10/09 15:26:08 INFO DAGScheduler: Final stage: ResultStage 0 (reduce at SparkPi.scala:38)
...
19/10/09 15:26:08 INFO TaskSchedulerImpl: Adding task set 0.0 with 2 tasks
19/10/09 15:26:08 INFO TaskSetManager: Starting task 0.0 in stage 0.0 (TID 0, localhost, executor driver, partition 0, PROCESS_LOCAL, 7866 bytes)
19/10/09 15:26:08 INFO TaskSetManager: Starting task 1.0 in stage 0.0 (TID 1, localhost, executor driver, partition 1, PROCESS_LOCAL, 7866 bytes)
19/10/09 15:26:08 INFO Executor: Running task 0.0 in stage 0.0 (TID 0) 19/10/09 15:26:08 INFO Executor: Running task 1.0 in stage 0.0 (TID 1)
19/10/09 15:26:08 INFO Executor: Fetching spark://lm-shb-24502336.paypalcorp.com:63772/jars/spark-examples_2.11-2.4.4.jar with timestamp 1570605967610
...
19/10/09 15:26:08 INFO Executor: Finished task 0.0 in stage 0.0 (TID 0). 824 bytes result sent to driver
19/10/09 15:26:08 INFO TaskSetManager: Finished task 0.0 in stage 0.0 (TID 0) in 292 ms on localhost (executor driver) (1/2)
19/10/09 15:26:08 INFO TaskSetManager: Finished task 1.0 in stage 0.0 (TID 1) in 274 ms on localhost (executor driver) (2/2)
19/10/09 15:26:08 INFO TaskSchedulerImpl: Removed TaskSet 0.0, whose tasks have all completed, from pool 
19/10/09 15:26:08 INFO DAGScheduler: ResultStage 0 (reduce at SparkPi.scala:38) finished in 0.538 s
19/10/09 15:26:08 INFO DAGScheduler: Job 0 finished: reduce at SparkPi.scala:38, took 0.600470 s
Pi is roughly 3.1421791421791423
...
19/10/09 15:26:08 INFO MemoryStore: MemoryStore cleared
19/10/09 15:26:08 INFO BlockManager: BlockManager stopped
19/10/09 15:26:08 INFO BlockManagerMaster: BlockManagerMaster stopped
19/10/09 15:26:08 INFO OutputCommitCoordinator$OutputCommitCoordinatorEndpoint: OutputCommitCoordinator stopped!
19/10/09 15:26:08 INFO SparkContext: Successfully stopped SparkContext
19/10/09 15:26:08 INFO ShutdownHookManager: Shutdown hook called

```

> Next section, we will go through the inner process of a spark applicaiton after submitted.
