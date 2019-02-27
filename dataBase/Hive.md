# Hive

## 大纲

1、熟练掌握hive的使用

2、熟练掌握hql的编写

3、理解hive的工作原理

4、具备hive应用实战能力

## Overview
[官网](http://hive.apache.org/)

[Hive介绍与核心知识点](https://mp.weixin.qq.com/s/8D3pemhEwOWfSjDAZAkLNg)

①什么是Hive?
The Apache Hive ™ data warehouse software facilitates reading, writing, and managing large datasets residing in distributed storage using SQL. Structure can be projected onto data already in storage. A command line tool and JDBC driver are provided to connect users to Hive.
apache的hive是一个数据参考的软件,它能方便提供非常方便的读、写和管理很大数据集,这个数据集是存储在分布式的存储系统上的，能够使用sql。已经存储在存储系统上的数据,可以用上结构化的东西,命令行的工具或jdbc的驱动能够连接到hive,

1. 大数据仓库,构建于Hadoop之上的数据仓库

2. 定义类SQL查询语言(HQL)

3. 将结构化的数据文件映射为一张数据库表

最初用于解决海量结构化的日志数据统计问题


②为什么使用Hive?

产生背景:

1. MapReduce编程的不便性

2. HDFS上的文件缺少Schema

好处:

1. 简单易上手(操作接口采用类SQL语法，提供快速开发的能力)

2. 避免了去写MapReduce，减少开发人员的学习成本

3. 为超大数据集设计的计算/存储扩展能力(MR计算、HDFS存储)

4. 统一的元数据管理(可与Presto/Impala/Spark SQL等共享数据)
    元数据管理(hive表名、表字段类型、分隔符等)

5. 扩展功能很方便

③可扩展(集群扩展)、延展性(自定义函数)、容错

## Hive架构

![image](https://github.com/leelovejava/doc/blob/master/img/dataBase/hive/01.png?raw=true)

Jobtracker是hadoop1.x中的组件，它的功能相当于： Resourcemanager+AppMaster

TaskTracker 相当于：  Nodemanager  +  yarnchild

### 基本组成
用户接口：包括 CLI、JDBC/ODBC、WebGUI。

元数据存储：通常是存储在关系数据库如 mysql , derby中。

解释器、编译器、优化器、执行器

### 各组件的基本功能
用户接口主要由三个：CLI、JDBC/ODBC和WebGUI。其中，CLI为shell命令行；JDBC/ODBC是Hive的JAVA实现，与传统数据库JDBC类似；WebGUI是通过浏览器访问Hive。

元数据存储：Hive 将元数据存储在数据库中。Hive 中的元数据包括表的名字，表的列和分区及其属性，表的属性（是否为外部表等），表的数据所在目录等。

解释器、编译器、优化器完成 HQL 查询语句从词法分析、语法分析、编译、优化以及查询计划的生成。生成的查询计划存储在 HDFS 中，并在随后有 MapReduce 调用执行。

### Hive与Hadoop的关系
Hive利用HDFS存储数据，利用MapReduce查询数据

用户:发送sql->hive:处理,转换成MapReduce,提交作业到Hadoop->Hadoop,运行作业->hdfs:存储

### Hive与传统数据库对比
![image](https://github.com/leelovejava/doc/blob/master/img/dataBase/hive/02.png?raw=true)

总结：hive具有sql数据库的外表，但应用场景完全不同，hive只适合用来做批量数据统计分析

### 数据存储

数据存储在hdfs上,没有专门的数据存储格式

在创建表的时候告诉 Hive 数据中的列分隔符和行分隔符,Hive 就可以解析数据

支持数据模型：DB、Table、External Table(外部表)、Partition(hdfs中table目录的字目录)、Bucket(桶)

## 定义

通常用于进行海量数据处理(采用MapReduce)

底层支持多种不同的执行引擎

支持多种不同的压缩格式、存储格式以及自定义函数

## 底层执行引擎

Hive底层的执行引擎有：MapReduce、Tez、Spark
	Hive on MapReduce
	Hive on Tez
	Hive on Spark
	
## 压缩/存储格式

压缩：GZIP、LZO、Snappy、BZIP2..
存储：TextFile、SequenceFile、RCFile、ORC、Parquet
UDF：自定义函数

    
## Hive环境搭建

### 方式
单机版、元数据库mysql版

[quick start](https://cwiki.apache.org/confluence/display/Hive/GettingStarted)
1）Hive下载：http://archive.cloudera.com/cdh5/cdh/5/
> wget http://archive.cloudera.com/cdh5/cdh/5/hive-1.1.0-cdh5.7.0.tar.gz

2）解压
> tar -zxvf hive-1.1.0-cdh5.7.0.tar.gz -C ~/app/

3）配置
	1. 系统环境变量(~/.bahs_profile)
		export HIVE_HOME=/home/hadoop/app/hive-1.1.0-cdh5.7.0
		export PATH=$HIVE_HOME/bin:$PATH
    2. 实现安装一个mysql， yum install xxx

	hive-site.xml
```
	<property>
  		<name>javax.jdo.option.ConnectionURL</name>
    	<value>jdbc:mysql://localhost:3306/sparksql?createDatabaseIfNotExist=true</value>
    </property>
    
	<property>
    	<name>javax.jdo.option.ConnectionDriverName</name>
        <value>com.mysql.jdbc.Driver</value>
   	</property>

	<property>
  		<name>javax.jdo.option.ConnectionUserName</name>
    	<value>root</value>
    </property>

	<property>
  		<name>javax.jdo.option.ConnectionPassword</name>
    	<value>root</value>
    </property>
```
4）拷贝mysql驱动到$HIVE_HOME/lib/

5）启动hive: $HIVE_HOME/bin/hive

## Hive使用
创建表
```
CREATE  TABLE table_name 
  [(col_name data_type [COMMENT col_comment])]
  

create table hive_wordcount(context string);
```

加载数据到hive表
```
LOAD DATA LOCAL INPATH 'filepath' INTO TABLE tablename 

load data local inpath '/home/hadoop/data/hello.txt' into table hive_wordcount;


select word, count(1) from hive_wordcount lateral view explode(split(context,'\t')) wc as word group by word;

lateral view explode(): 是把每行记录按照指定分隔符进行拆解
```

hive ql提交执行以后会生成mr作业，并在yarn上运行
```
create table emp(
    empno int,
    ename string,
    job string,
    mgr int,
    hiredate string,
    sal double,
    comm double,
    deptno int
) ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t';

create table dept(
    deptno int,
    dname string,
    location string
) ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t';

load data local inpath '/home/hadoop/data/emp.txt' into table emp;
load data local inpath '/home/hadoop/data/dept.txt' into table dept;

求每个部门的人数
select deptno, count(1) from emp group by deptno;
```

## 面试题

[面试题整理(Hive)](https://blog.csdn.net/wxfghy/article/details/81361400)

1. Hive数据倾斜
原因
    key分布不均匀
    业务数据本身的特性
    SQL语句造成数据倾斜
解决方法
    hive设置hive.map.aggr=true和hive.groupby.skewindata=true
    有数据倾斜的时候进行负载均衡，当选项设定为true,生成的查询计划会有两个MR Job。第一个MR Job中，Map的输出结果集合会随机分布到Reduce中，每个Reduce做部分聚合操作，并输出结果，这样处理的结果是相同Group By Key有可能被分发到不同的Reduce中，从而达到负载均衡的目的；第二个MR Job在根据预处理的数据结果按照 Group By Key 分布到Reduce中(这个过程可以保证相同的 Group By Key 被分布到同一个Reduce中)，最后完成最终的聚合操作。
    SQL语句调整: 
        A. 选用join key 分布最均匀的表作为驱动表。做好列裁剪和filter操作，以达到两表join的时候，数据量相对变小的效果。
        B. 大小表Join： 使用map join让小的维度表（1000条以下的记录条数）先进内存。在Map端完成Reduce。
        C. 大表Join大表：把空值的Key变成一个字符串加上一个随机数，把倾斜的数据分到不同的reduce上，由于null值关联不上，处理后并不影响最终的结果。
        D. count distinct大量相同特殊值：count distinct时，将值为空的情况单独处理，如果是计算count distinct，可以不用处理，直接过滤，在做后结果中加1。如果还有其他计算，需要进行group by，可以先将值为空的记录单独处理，再和其他计算结果进行union.

2. Hive中的排序关键字有哪些

sort by ，order by ，cluster by ，distribute by

sort by ：不是全局排序，其在数据进入reducer前完成排序

order by ：对输入做全局排序，因此只有一个reducer(多个reducer无法保证全局有序).只有一个reducer,会导致当输入规模较大时，需要较长的计算时间。

distribute by ：按照指定的字段对数据进行划分输出到不同的reduce中

cluster by：除了具有 distribute by 的功能外还兼具 sort by 的功能

* sort by和order by之间的区别？

order by:全局排序

distribute和sort:分组排序

3. 海量数据分布在100台电脑中，想个办法高效统计出这批数据的TOP10
方案1:
    在每台电脑上求出TOP10，可以采用包含10个元素的堆完成(TOP10小，用最大堆，TOP10大，用最小堆)。
    比如求TOP10大，我们首先取前10个元素调整成最小堆，如果发现，然后扫描后面的数据，并与堆顶元素比较，如果比堆顶元素大，那么用该元素替换堆顶，然后再调整为最小堆。
    最后堆中的元素就是TOP10大。
方案2
    求出每台电脑上的TOP10后，然后把这100台电脑上的TOP10组合起来，共1000个数据
    再利用上面类似的方法求出TOP10就可以了。
    
4. Hive中追加导入数据的4种方式是什么？请写出简要语法
    从本地导入： load data local inpath ‘/home/1.txt’ (overwrite)into table student;
    
    从Hdfs导入： load data inpath ‘/user/hive/warehouse/1.txt’ (overwrite)into table student;
    
    查询导入： create table student1 as select * from student;(也可以具体查询某项数据)
    
    查询结果导入：insert （overwrite）into table staff select * from track_log;
    
5. Hive导出数据有几种方式？如何导出数据

①用insert overwrite导出方式 

    A. 导出到本地： 
    
    insert overwrite local directory ‘/home/robot/1/2’ rom format delimited fields terminated by ‘\t’ select * from staff;(递归创建目录)

    B. 导出到HDFS 
    
    insert overwrite directory ‘/user/hive/1/2’ rom format delimited fields terminated by ‘\t’ select * from staff;

②Bash shell覆盖追加导出 

例如：$ bin/hive -e “select * from staff;” > /home/z/backup.log

③Sqoop把hive数据导出到外部

6. hive 内部表和外部表区别

内部表：加载数据到 hive 所在的 hdfs 目录，删除时，元数据和数据文件都删除

外部表：不加载数据到 hive 所在的 hdfs 目录，删除时，只删除表结构

* 表类型

内部表、外部表、分区表、桶表

* 创建表方式
create + load、like + load、as 创建表的同时加载数据、create + insert

7. 分区和分桶的区别
分区
    是指按照数据表的某列或某些列分为多个区，区从形式上可以理解为文件夹，比如我们要收集某个大型网站的日志数据，一个网站每天的日志数据存在同一张表上，由于每天会生成大量的日志，导致数据表的内容巨大，在查询时进行全表扫描耗费的资源非常多。
    
    那其实这个情况下，我们可以按照日期对数据表进行分区，不同日期的数据存放在不同的分区，在查询时只要指定分区字段的值就可以直接从该分区查找

分桶
    分桶是相对分区进行更细粒度的划分。
    
    分桶将整个数据内容安装某列属性值得hash值进行区分，如要按照name属性分为3个桶，就是对name属性值的hash值对3取摸，按照取模结果对数据分桶。
    
    如取模结果为0的数据记录存放到一个文件，取模为1的数据存放到一个文件，取模为2的数据存放到一个文件

分区作用:防止数据倾斜

8. Hive的分组方式

row_number() 是没有重复值的排序(即使两天记录相等也是不重复的),可以利用它来实现分页
dense_rank() 是连续排序,两个第二名仍然跟着第三名
rank()       是跳跃排序的,两个第二名下来就是第四名
    
9. Hive优化

①通用设置
    hive.optimize.cp=true：列裁剪 
    hive.optimize.prunner：分区裁剪 
    hive.limit.optimize.enable=true：优化LIMIT n语句 
    hive.limit.row.max.size=1000000： 
    hive.limit.optimize.limit.file=10：最大文件数

②本地模式(小任务)
    job的输入数据大小必须小于参数：hive.exec.mode.local.auto.inputbytes.max(默认128MB)
    job的map数必须小于参数：hive.exec.mode.local.auto.tasks.max(默认4)
    job的reduce数必须为0或者1 
    hive.exec.mode.local.auto.inputbytes.max=134217728 
    hive.exec.mode.local.auto.tasks.max=4 
    hive.exec.mode.local.auto=true 
    hive.mapred.local.mem：本地模式启动的JVM内存大小

③并发执行
hive.exec.parallel=true ，默认为false 
hive.exec.parallel.thread.number=8

④Strict Mode：
hive.mapred.mode=true，严格模式不允许执行以下查询： 
    
    分区表上没有指定了分区
    
    没有limit限制的order by语句
    
    笛卡尔积：JOIN时没有ON语句

⑤动态分区
hive.exec.dynamic.partition.mode=strict：该模式下必须指定一个静态分区 
hive.exec.max.dynamic.partitions=1000 
hive.exec.max.dynamic.partitions.pernode=100：在每一个mapper/reducer节点允许创建的最大分区数 
DATANODE：dfs.datanode.max.xceivers=8192：允许DATANODE打开多少个文件

⑥推测执行
    mapred.map.tasks.speculative.execution=true 
    mapred.reduce.tasks.speculative.execution=true 
    hive.mapred.reduce.tasks.speculative.execution=true;

⑦多个group by合并
hive.multigroupby.singlemar=true：当多个GROUP BY语句有相同的分组列，则会优化为一个MR任务

⑧虚拟列
hive.exec.rowoffset：是否提供虚拟列

⑨分组
两个聚集函数不能有不同的DISTINCT列，以下表达式是错误的： 
INSERT OVERWRITE TABLE pv_gender_agg SELECT pv_users.gender, count(DISTINCT pv_users.userid), count(DISTINCT pv_users.ip) FROM pv_users GROUP BY pv_users.gender;
SELECT语句中只能有GROUP BY的列或者聚集函数。

⑩Combiner聚合
hive.map.aggr=true;在map中会做部分聚集操作，效率更高但需要更多的内存。 
hive.groupby.mapaggr.checkinterval：在Map端进行聚合操作的条目数目

⑪数据倾斜
㈠hive.groupby.skewindata=true：数据倾斜时负载均衡，当选项设定为true，生成的查询计划会有两个MRJob。

㈡第一个MRJob 中，Map的输出结果集合会随机分布到Reduce中，每个Reduce做部分聚合操作，并输出结果，这样处理的结果是相同的GroupBy Key 
有可能被分发到不同的Reduce中，从而达到负载均衡的目的；

㈢第二个MRJob再根据预处理的数据结果按照GroupBy Key分布到Reduce中（这个过程可以保证相同的GroupBy Key被分布到同一个Reduce中），最后完成最终的聚合操作。

⑫排序
    ORDER BY colName ASC/DESC 
    hive.mapred.mode=strict时需要跟limit子句 
    hive.mapred.mode=nonstrict时使用单个reduce完成排序 
    SORT BY colName ASC/DESC ：每个reduce内排序 
    DISTRIBUTE BY(子查询情况下使用 )：控制特定行应该到哪个reducer，并不保证reduce内数据的顺序 
    CLUSTER BY ：当SORT BY 、DISTRIBUTE BY使用相同的列时。

⑬合并小文件
    hive.merg.mapfiles=true：合并map输出 
    hive.merge.mapredfiles=false：合并reduce输出 
    hive.merge.size.per.task=256*1000*1000：合并文件的大小 
    hive.mergejob.maponly=true：如果支持CombineHiveInputFormat则生成只有Map的任务执行merge 
    hive.merge.smallfiles.avgsize=16000000：文件的平均大小小于该值时，会启动一个MR任务执行merge。

⑭自定义map/reduce数目

㈠减少map数目： 
　　set mapred.max.split.size 
　　set mapred.min.split.size 
　　set mapred.min.split.size.per.node 
　　set mapred.min.split.size.per.rack 
　　set hive.input.format=org.apache.hadoop.hive.ql.io.CombineHiveInputFormat

㈡增加map数目： 
⑴当input的文件都很大，任务逻辑复杂，map执行非常慢的时候，可以考虑增加Map数，来使得每个map处理的数据量减少，从而提高任务的执行效率。

⑵假设有这样一个任务： 
select data_desc, count(1), count(distinct id),sum(case when …),sum(case when ...),sum(…) from a group by data_desc

⑶如果表a只有一个文件，大小为120M，但包含几千万的记录，如果用1个map去完成这个任务，肯定是比较耗时的，这种情况下，我们要考虑将这一个文件合理的拆分成多个，这样就可以用多个map任务去完成。 
　　set mapred.reduce.tasks=10; 
　　create table a_1 as select * from a distribute by rand(123);

⑷这样会将a表的记录，随机的分散到包含10个文件的a_1表中，再用a_1代替上面sql中的a表，则会用10个map任务去完成。每个map任务处理大于12M（几百万记录）的数据，效率肯定会好很多。

㈢reduce数目设置： 

参数1：hive.exec.reducers.bytes.per.reducer=1G：每个reduce任务处理的数据量

参数2：hive.exec.reducers.max=999(0.95*TaskTracker数)：每个任务最大的reduce数目

reducer数=min(参数2,总输入数据量/参数1)

set mapred.reduce.tasks：每个任务默认的reduce数目。典型为0.99*reduce槽数，hive将其设置为-1，自动确定reduce数目。

⑮使用索引：
hive.optimize.index.filter：自动使用索引 
hive.optimize.index.groupby：使用聚合索引优化GROUP BY操作