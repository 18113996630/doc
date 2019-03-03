# Apache Flink

Apache Flink is an open source stream processing framework with powerful stream- and batch-processing capabilities.

Learn more about Flink at [http://flink.apache.org/](http://flink.apache.org/)

## doc
[阿里巴巴为什么选择Apache Flink？](https://mp.weixin.qq.com/s/AoSDPDKbTbjH9rviioK-5Q)

[阿里重磅开源Blink：为什么我们等了这么久？](https://yq.aliyun.com/articles/680813)

[基于 Apache Flink 的实时计算引擎 Blink 在阿里搜索中的应用](https://yq.aliyun.com/articles/183934)

[Apache Flink 干货合集打包](https://mp.weixin.qq.com/s/rsJlZEP_oVG3NiFRyeS8gw)

[Apache Flink数据流容错机制](https://www.iteblog.com/archives/1987.html)

[美团点评基于 Flink 的实时数仓建设实践](https://mp.weixin.qq.com/s/PJmdXkdUE5gtzcYAgAM8wQ)

[四种优化 Apache Flink 应用程序的方法](https://www.iteblog.com/archives/2303.html)

[Flink on YARN部署快速入门指南](https://www.iteblog.com/archives/1620.html)

[flink的神奇分流器-sideoutput](https://mp.weixin.qq.com/s/nrBT2xkXpY8MmEksLessIw)

[Flink特异的迭代操作-bulkIteration](https://mp.weixin.qq.com/s/b5aCHqyhN1gO2OeYXdGG5g)

[Flink Forward China 2018](https://github.com/zheniantoushipashi/2018-flink-forward-china)

[《从0到1学习Flink》—— Flink 写入数据到 ElasticSearch](https://segmentfault.com/a/1190000017874148)

[写在阿里Blink正式开源之际](https://mp.weixin.qq.com/s/Sx2k0jS7bl6DZAlWlTAcsA)

[用Flink取代Spark Streaming！知乎实时数仓架构演进](https://mp.weixin.qq.com/s/kN93pMYTxeIRZv5wWu7-ug)

## Features

* A streaming-first runtime that supports both batch processing and data streaming programs

* Elegant and fluent APIs in Java and Scala

* A runtime that supports very high throughput and low event latency at the same time

* Support for *event time* and *out-of-order* processing in the DataStream API, based on the *Dataflow Model*

* Flexible windowing (time, count, sessions, custom triggers) across different time semantics (event time, processing time)

* Fault-tolerance with *exactly-once* processing guarantees

* Natural back-pressure in streaming programs

* Libraries for Graph processing (batch), Machine Learning (batch), and Complex Event Processing (streaming)

* Built-in support for iterative programs (BSP) in the DataSet (batch) API

* Custom memory management for efficient and robust switching between in-memory and out-of-core data processing algorithms

* Compatibility layers for Apache Hadoop MapReduce and Apache Storm

* Integration with YARN, HDFS, HBase, and other components of the Apache Hadoop ecosystem


## Streaming Example
```scala
case class WordWithCount(word: String, count: Long)

val text = env.socketTextStream(host, port, '\n')

val windowCounts = text.flatMap { w => w.split("\\s") }
  .map { w => WordWithCount(w, 1) }
  .keyBy("word")
  .timeWindow(Time.seconds(5))
  .sum("count")

windowCounts.print()
```

## Batch Example
```scala
case class WordWithCount(word: String, count: Long)

val text = env.readTextFile(path)

val counts = text.flatMap { w => w.split("\\s") }
  .map { w => WordWithCount(w, 1) }
  .groupBy("word")
  .sum("count")

counts.writeAsCsv(outputPath)
```



## Building Apache Flink from Source

Prerequisites for building Flink:

* Unix-like environment (we use Linux, Mac OS X, Cygwin)
* git
* Maven (we recommend version 3.2.5)
* Java 8 (Java 9 and 10 are not yet supported)

```
git clone https://github.com/apache/flink.git
cd flink
mvn clean package -DskipTests # this will take up to 10 minutes
```

Flink is now installed in `build-target`

*NOTE: Maven 3.3.x can build Flink, but will not properly shade away certain dependencies. Maven 3.0.3 creates the libraries properly.
To build unit tests with Java 8, use Java 8u51 or above to prevent failures in unit tests that use the PowerMock runner.*

## Developing Flink

The Flink committers use IntelliJ IDEA to develop the Flink codebase.
We recommend IntelliJ IDEA for developing projects that involve Scala code.

Minimal requirements for an IDE are:
* Support for Java and Scala (also mixed projects)
* Support for Maven with Java and Scala


## IntelliJ IDEA

The IntelliJ IDE supports Maven out of the box and offers a plugin for Scala development.

* IntelliJ download: [https://www.jetbrains.com/idea/](https://www.jetbrains.com/idea/)
* IntelliJ Scala Plugin: [http://plugins.jetbrains.com/plugin/?id=1347](http://plugins.jetbrains.com/plugin/?id=1347)

Check out our [Setting up IntelliJ](https://github.com/apache/flink/blob/master/docs/internals/ide_setup.md#intellij-idea) guide for details.

## Eclipse Scala IDE

**NOTE:** From our experience, this setup does not work with Flink
due to deficiencies of the old Eclipse version bundled with Scala IDE 3.0.3 or
due to version incompatibilities with the bundled Scala version in Scala IDE 4.4.1.

**We recommend to use IntelliJ instead (see above)**

## Support

Don’t hesitate to ask!

Contact the developers and community on the [mailing lists](http://flink.apache.org/community.html#mailing-lists) if you need any help.

[Open an issue](https://issues.apache.org/jira/browse/FLINK) if you found a bug in Flink.


## Documentation

The documentation of Apache Flink is located on the website: [http://flink.apache.org](http://flink.apache.org)
or in the `docs/` directory of the source code.


## Fork and Contribute

This is an active open-source project. We are always open to people who want to use the system or contribute to it.
Contact us if you are looking for implementation tasks that fit your skills.
This article describes [how to contribute to Apache Flink](http://flink.apache.org/how-to-contribute.html).


## About

Apache Flink is an open source project of The Apache Software Foundation (ASF).
The Apache Flink project originated from the [Stratosphere](http://stratosphere.eu) research project.
