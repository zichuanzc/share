# Spark基础Python版
## 第1章 大数据技术概述
### 大数据时代
    第三次信息化浪潮
        标志：云计算、物联网、大数据
    技术支持：
        存储设备：价格降低、容量增大
        CPU处理能力：大幅度提升
        网络带宽：不断增加，分布式、集群运算
    数据产生方式变革：
        运营式系统阶段---》用户原创内容阶段---》感知系统阶段
        
### 大数据概念 4V
    大量化(Volume)：大数据摩尔定律
        字节大小：Byte < KB < MB < GB < TB < PB < EB < ZB
    快速化(Velocty)：一秒定律
    多样化(Variety)
    价值密度低(Value)

### 大数据的影响
    科学研究范式发展：试验、理论、计算、数据
    思维方式：全样而非抽样、效率而非精确、相关而非因果

### 大数据关键技术
    数据采集
    数据存储与管理：分布式存储，解决数据存储问题
    数据处理与分析：分布式处理，解决数据高效计算问题
    数据隐私与安全

### 大数据计算模式
    不同的计算模式需要试验不同的产品
    典型的计算模式：
        1.批处理计算：Mapreduce、Spark
        2.流计算：F4、Storm、Flume
        3.图计算：Google Pregel
        4.查询分析计算：Googel Dremel、Hive、Cassandra

### 代表性大数据
#### 1.Hadoop
    Ambari：安装、部署、配置和管理工具
    Hive、Pig、Mahout：数据仓库、数据流处理、数据挖掘库
    Mapreduce：分布式计算框架
        分而治之：分片输入、Map任务、Reduce任务、输出
    YARN：资源调度和管理框架，实现"一个急缺多个框架"
    HDFS：分布式文件系统
##### Hadoop生态环境
![Hadoop生态环境](2022-10-22-08-17-39.png "Hadoop生态环境")
#### 2.Spark
    批处理、流处理、机器算法、图算法
    Spark VS Hadoop：
        Hadoop：表达能力有限、磁盘IO开销大、延迟高
        Spark：本质也是map、reduce，是一个单纯的计算框架，不提供存储能力
            操作类型多、流水化执行、效率高
        Spark会取代Hadoop吗：不会，Spark只相当于Hadoop中的Mapreduce
##### Spark架构图
![Spark架构图](2022-10-22-08-22-59.png "Spark架构图")
#### 3.Flink和Beam
##### Flink与Spark进行比较
![Flink和Spark比较](2022-10-22-08-28-33.png "Flink和Spark比较")
##### Beam：Googel开发，想统一大数据开发


## 第2章 Spark的设计与运行原理
### Spark概述
    AMP实验室：对mapreduce缺陷改进
    特点：
        运行速度块：内存计算、循环数据流、DAG计算执行流程
        容易使用：Scala、Python、Java、R语言...
        通用性：
            SQL查询：spark SQL
            流式计算：spark streaming
            机器学习：spark MLlib
            图算法组件：spark的Graph X
        运行模式多样：运行于独立的集群模式
    和Hadoop的mapreduce比较：
        操作类型多：表达能力更强
        尽量使用内存计算：运行效率提升
        DAG有向无环图：流水线优化

### Spark生态系统
#### 1.典型应用场景
![](2022-10-22-08-38-20.png "spark典型应用场景")
#### 2.spark出现之前
    各种处理：
        批处理：Hadoop mapreduce
        交互查询：apache
        流处理：apache storm
    问题：相互之间需要进行数据格式转换、维护成本较高、资源利用不充分
#### 3.Spark：一个软件栈满足不同场景的需求
 ![](2022-10-22-08-41-56.png "spark各种应用场景")

### 4.BDAS：伯克利数据分析软件栈
![](2022-10-22-08-43-04.png "伯克利数据分析软件栈")

### 基本概念和架构设计
#### 1.基本概念：RDD是最核心的抽象概念
![](2022-10-22-08-44-54.png "spark基本概念")
##### 基本概念相互之间关系
![](2022-10-22-08-46-27.png "基本概念相互之间关系")
#### 2.架构：一组多重
 ![](2022-10-22-08-47-41.png "spark架构图示")

### Spark运算基本流程
![](2022-10-22-08-48-25.png "Spark运算基本流程")

### RDD运算原理
#### 1.RDD设计背景
    迭代场景：IO、序列化和反序列化开销
    分布式对象的集合、只读：高度受限的共享内存模型，转换过程中可以修改
    分布式进行处理
#### 2.操作类型：粗粒度的
    动作类型：action
    转换类型：translate
#### 3.特性
    1.能力强
    2.高度受限的共享内存模型
    3.不会影响表达能力
    4.惰性调用机制
    5.流水线优化：避免不必要的IO、序列化和反序列化开销
    6.高效的容错性：
        现有：数据复制(备份)、记录日志(事务机制)
        spark天然的容错性：
            Lineage(血缘关系)：恢复数据、寻亲
            持久化内存
            避免不必要的序列化和反序列化
##### 惰性调用机制图示
![](2022-10-22-08-59-25.png "惰性调用机制图示")
##### Lineage恢复数据图示
![](2022-10-22-09-00-25.png "Lineage恢复数据图示")
#### 4.RDD之间的依赖关系
    应用、作业、阶段、任务
    宽依赖：
        一个父RDD对应多个子RDD
        例子：Shuffle(洗牌)操作，大量网络数据分支
    窄依赖：
        一个父亲对应一个儿子、多个父亲对应一个儿子
#### 5.阶段的划分和RDD运行过程
    优化原理Fork/Join机制：
        窄依赖(Join)：可以进行流水线优化
        宽依赖(Fork)：不能进行流水线优化
    不发生无意义的等待
    只要发送shuffle一定会写磁盘
    DAG图反向解析：
        不断加入阶段：窄依赖
        宽依赖：生成不同阶段
##### RDD运行过程图示
![](2022-10-22-09-08-40.png "RDD运行过程图示")
#### 6.Spark的部署和应用方式
    1.单机：local模式
    2.集群:
        sandalone:spark自带，效率不高
        YARN
        Mesos
    spark相当于Hadoop的计算框架mapreduce：
        Hadoop：HDFS、HBase、MapReduce


## 第3章 Spark环境搭建和使用方法
### 安装spark
    基础环境配置：Linux、Java、Hadoop
[Hadoop安装教程_单机/伪分布式配置_Hadoop2.6.0/Ubuntu14.04](http://dblab.xmu.edu.cn/blog/iinstall-hadoop/)
### spark下载
[Spark安装包下载地址](http://spark.apache.org)

### spark部署模式
    单机、伪分布式、集群

### 在pyspark中运行代码
    pyspark提供了Python交互式执行环境


## 第4章 RDD编程
## 第5章 Spark SQL
## 第6章 Spark Streaming
## 第7章 Structured Streaming
## 第8章 Spark MLlib