<!--
  author:donar
  head:http://www.easyicon.net/api/resizeApi.php?id=556429&size=128
  date:2016-06-12
  title:web系统性能监控
  tags:web,性能
  category:技术笔记
  status:publish
  summary:web性能监控指标
-->

性能测试需要使用不同的工具，结合系统日志，监控服务器、应用等方面的多项指标。以下阐述监控指标、监控工具、瓶颈分析。

<>服务端监控指标
---------

性能测试通常需要监控的指标包括：

服务器 Linux（包括CPU、Memory、Load、I/O）。

数据库：[Mysql](http://lib.csdn.net/base/14)（缓存命中、索引、单条SQL性能、数据库线程数、数据池连接数）。

中间件：1.tomcat 2、nginx 3、memcache（包括线程数、连接数、日志）。

网络： 吞吐量、吞吐率。

应用： jvm内存、日志、Full GC频率。

<>客户端监控指标
---------

LoadRunner：用户执行情况、场景状态、事务响应时间、TPS、吞吐量等。

测试机资源：CPU、Memory、网络、磁盘空间。

<>常用监控工具
--------

**Jstat**

**
**

监控java 进程GC情况，判断GC是否正常。

**JConsole**

**
**

监控java内存、javaCPU使用率、线程执行情况等，需要在JVM参数中进行配置。

**JMap**

**
**

监控java程序是否有内存泄漏，需要配合eclipse插件或者MemoryAnalyzer来使用。

**JProfiler**

**
**

全面监控每个节点的CPU使用率、内存使用率、响应时间累计值、线程执行情况等，需要在JVM参数中进行配置。

**Nmon**

**
**

全面监控linux系统资源使用情况，包括CPU、内存、I/O等，可独立于应用监控。

**Probe**

**
**

全面监控tomcat的线程、内存、JVM CPU 使用率、OS 和 JVM内存使用率、交换区使用率、每30秒内接收到的请求数目等等

**Memadim**

**
**

1\. 服务器参数监控：STATS、SETTINGS、ITEMS、SLABS、SIZES实时刷新

2\. 服务器性能监控：GET、DELETE、INCR、DECR、CAS等常用操作命中率实时监控

3\. 支持数据遍历，方便对存储内容进行监视

4\. 支持条件查询，筛选出满足条件的KEY或VALUE

<>性能分析
------

**分析信息来源**

**
**

5\. 监控工具所采集的信息。包括TPS、响应时间、用户并发数、JVM内存、Full GC频率、tomcat连接数，数据sql执行时间、memcache的命中率、nginx的连接数等。

6\. 应用服务器的日志。包括错误日志、超时日志等。

7\. 项目配合人员所提供的信息。包括DBA提供的数据库监控信息、开发人员提供的代码逻辑信息。

**分析标准**

**
**

1.通过性能指标的表现形式，分析性能是否稳定。比如：

2.响应时间是否符合性能预期，表现是否稳定。

3.应用日志中，超时的概率，是否在可接受的范围之内。

8\. TPS维持在多大的范围内，是否有波形出现，标准差有多少，是否符合预期。

9\. 服务器CPU、内存、load是否在合理的范围内，等等。

**分析工具**

**
**

对于部分性能指标，可借助自动分析工具，统计出数据的总体趋势：

1、LoadRunner analysis 分析

LoadRunneranalysis是loadrunner的一个部件，用于将运行过程中所采集到的数据生成报表，主要用于采集TPS、响应时间、吞吐量、服务器资源使用情况等变化趋势。

2、Memory Analyzer分析

Memory Analyzer工具可以解析Jmap dump出来的内存信息，查找是否有内存泄漏。

3、nmon\_analyser分析

nmon工具可以采集服务器的资源信息。列出CPU、MEM、网络、I/O等资源指标的使用情况。

4、MONyog分析

通过此工具我们能够跟踪到执行比较慢的sql语句，并且可以分析出sql语句执行时扫描的行数，使用的索引情况。
