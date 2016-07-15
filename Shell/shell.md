<!--
  author:fasiy
  head:http://www.easyicon.net/api/resizeApi.php?id=556429&size=128
  date:2016-07-12
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

#案例二
####shell的管道技巧-while read line
**在Bash Shell中，管道的最后一个命令都是在子Shell中执行的。**这意味着在子Shell中赋值的变量对父Shell是无效的。所以当我们将管道输出传送到一个循环结构，填入随后将要使用的变量，那么就会产生很多问题。一旦循环完成，其所依赖的变量就不存在了。
 
直接上代码：
test文件只是一个空行，意味着每一个循环体都会执行一次。

```shell
    #!/bin/bash
    t=0
    while read line;do
    {
      t=2
    }
    done < test
    
    echo "1:"${t}
    cat test| while read line;
    do
      echo "2:"${t}   #已经是在子进程内了，子进程会将父进程进行一份拷贝。
      t=3
      echo "3:"${t}   #子进程的值的改变不会影响父进程的值。
    done
    echo "4:"${t}     #打印父进程的值
```
最终的输出结果很显而易见了，读者自己去试试吧。说道这里顺带梳理下子父进程的关系。
每当我们在一个shell里执行一个脚本程序的时候，该shell都会fork出一个新进程，从而启动另一个命令解释器来解释执行我们的脚本。新进程就是一个子shell，而之前的shell则是一个父shell。

在我们所运行的脚本里，我们还可以启动新的子 shell 进程，这些子 shell 进程使脚本并行地运行着多个子任务。一般而言，在一个脚本里执行一个外部命令(普通的可执行文件)时，shell 会 fork 出一个子进程，然后再用 exec 来执行这个程序；但是，bash shell 的内置命令(builtin)却不会这样，它们是直接执行的。所以，等价的内置命令的执行速度会比执行外部命令要来的快。

这里埋下几个遗留问题，后续文档进行解决。  
1.  哪些情况下会产生新的子进程。  
2.  进程间通信——如何让子进程跟父进程进行通信。  
