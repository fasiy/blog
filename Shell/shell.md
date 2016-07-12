<!--
  author:donar
  head:http://www.easyicon.net/api/resizeApi.php?id=556429&size=128
  date:2016-06-17
  title: shell编程踩坑总结
  tags:shell
  category:技术笔记
  status:publish
  summary:shell
-->

#案例一
####shell的输入重定向
现有文件

```shell
	xxx@SP3suse36:~/test> cat list.txt 
	10.75.155.36 test 
	10.75.162.140 test 
```
前提：当前用户已经与其他两台机器建立好了互信关系。
需求：在当前机器遍历其他两台机器执行某个命令。
解决方案一：

```shell
csc.sh
	#!/bin/bash
	scriptPath=$(cd $(dirname ${BASH_SOURCE:-$0});pwd)
	list=${scriptPath}/list.txt
	cat ${list}|while read ip user;do
	    ssh ${user}@${ip} "ls"
	done
```
执行效果：

```shell
	sh csc.sh
	bin
```
结果分析

```
	ERROR！最终结果只循环执行了一次。
```
解决方案二：

```shell
getLs.sh
    #!/bin/bash
    scriptPath=$(cd $(dirname ${BASH_SOURCE:-$0});pwd)
    list=${scriptPath}/list.txt
    exec 10<${list}
    while read -u 10 ip user;do
        ssh ${user}@${ip} "ls"
    done
```
执行效果:

```shell
    sh getLs.sh
    bin
    bin
```
结果分析：

```
    Correct！
```
解决方案三：

```shell
getForLs.sh
    #!/bin/bash
    scriptPath=$(cd $(dirname ${BASH_SOURCE:-$0});pwd)
    list=${scriptPath}/list.txt
    for line in `sed 's/ /,/g' ${list}`;do
        ip=$(echo ${line}|awk -F "," '{print $1}')
        user=$(echo ${line}|awk -F "," '{print $2}')
        ssh ${user}@${ip} "ls"
    done
```
执行效果:

```shell
    sh getForLs.sh
    bin
    bin
```
结果分析：

```
    Correct！
```
总结：

```
使用while直接读取只能读取一行，但是使用for却可以成功，非常奇怪。
经过查证，while中使用了重定向机制，整个list.txt文件中的信息都已经读入并重定向给了整个while语句。当我们在while循环中调用一次read语句的时候，它会读取内存中下一条记录。而ssh语句正好会读取输入中的所有东西——将重定向给while语句的list.txt全部读走了。将上述ssh登录语句改为ssh ${user}@${ip} "cat"，你会发现list.txt的其他行都被打印出来了。由此可以得出解决方案四，重定向ssh的输入，可以使用-n或者< /dev/null。
```
解决方案四：

```shell
csc.sh
    #!/bin/bash
    scriptPath=$(cd $(dirname ${BASH_SOURCE:-$0});pwd)
    list=${scriptPath}/list.txt
    cat ${list}|while read ip user;do
        ssh ${user}@${ip} "ls" < /dev/null
    done
```