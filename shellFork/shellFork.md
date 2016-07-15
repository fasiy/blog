<!--
  author:fasiy
  head:http://www.easyicon.net/api/resizeApi.php?id=556429&size=128
  date:2016-07-15
  title: shell什么时候会创建新进程
  tags:shell进程
  category:技术笔记
  status:publish
  summary:shell什么时候会创建新进程
-->
#shell什么时候会创建新进程
以下是我能想出来的一部分
>1. ()会新开一个子进程（注意{}不会新开一个子进程）
>2. &会新开一个子进程——用它可以实现简单的shell的异步并发
>3. 执行非内置命令（read, set, export）——其实就是执行外部命令和你的shell脚本。
>4. |管道两边都时新开进程
```shell
#!/bin/bash
a="hello world"
echo "$a"| read var
echo $var
exit 0
```
ATTETION！运行输出为空，因为它是在子进程被获取的。

#如何检验是否是创建新进程了呢
1. 在新进程和父进程利用一个变量进行赋值，赋值完成后打印值结果，不一样那就说明不是同一个进程。
2. 使用$$查看当前进程号，但是直接在终端验证貌似不大好用，因为解释器会先将这个字符解释后再执行命令，导致你看到的都是一样的。
