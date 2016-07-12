<!--
  author:donar
  head:http://www.easyicon.net/api/resizeApi.php?id=556429&size=128
  date:2016-06-12
  title: mysql 批量插入问题
  tags:mysql,性能
  category:技术笔记
  status:publish
  summary:mysql 批量插入性能优化
-->

第一，MySql的JDBC连接的url中要加rewriteBatchedStatements参数，并保证5.1.13以上版本的驱动，才能实现高性能的批量插入。

Mysql JDBC驱动，各个版本测试结果：

MySql JDBC 驱动版本号 插入10万条数据用时

5.0.8 加了rewriteBatchedStatements参数，没有提高还是17.4秒

5.1.7 加了rewriteBatchedStatements参数，没有提高还是17.4秒

5.1.13 加了rewriteBatchedStatements参数，插入速度提高到1.6秒

第二，保证表插入的顺序，如果要批量插入A和B两个表的数据，要保证先批量插入表A，再批量插入表B。原因在于磁盘IO，应为MySQL会保证SQL的顺序性，不断在A,B表之间切换，相当与磁盘寻址要不断改变，相当损耗性能，这无异于单条插入。

第三，小心事务嵌套，jdbc Rewrite Batched Statements批量插入要求在一个事务中进行批量插
