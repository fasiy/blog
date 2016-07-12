<!--
  author:donar
  head:http://www.easyicon.net/api/resizeApi.php?id=556429&size=128
  date:2016-06-17
  title: OOM处理步骤
  tags:oom,java
  category:技术笔记
  status:publish
  summary:java oom 处理步骤
-->

OOM处理步骤

1.jps -v |grep ${projectName} 查看当前用户的java进程 

2.jstat -gcutil ${pid} 1000 5 看哪个区oom了

3.jmap -histo ${pid} |grep XXX 看哪个类的实例多 

4.在代码中定位OOM的原因
