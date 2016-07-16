<!--
  author:fasiy
  head:http://www.easyicon.net/api/resizeApi.php?id=556429&size=128
  date:2016-07-15
  title: 多进程通信
  tags:多进程通信
  category:技术笔记
  status:publish
  summary:多进程通信
-->
#多进程通信
现在在玩shell，所以就从shell脚本为切入点聊聊多进程通信。
##论点
1. 父shell创建出来的子shell实际是父shell的一个拷贝。
2. 使用export命令可以将父shell的变量导出提供给子shell使用，逆向不成立。
3. 使用export导出的变量实际是一个拷贝，被子shell随意修改也不会改变父shell的变量的值。  

##基于以上论点，如何实现儿子与爸爸的对话呢
1.使用文件

```
子进程将结果输出到文件系统，然后父进程去读取文件。
```
2.使用``或者$()命令替换

```
a=$(xxxx)或者a=`xxxx`，这是一种将命令执行后的标准输出放到变量里面去，很常用哦。
```
3.使用命名管道

```shell
#!/bin/bash
mkfifo -m 777 npipe
(
  subsend="hello world"
  echo "$subsend" > npipe &
 )
read pread < npipe
echo "$pread"
exit 0
```
4.使用 here 文档

```shell
#!/bin/bash
read pvar << HERE
`subvar="hello shell"
echo $subvar`
HERE
echo $pvar
```

----
先总结到这四种方法，这些本质上都是进程间通信的方法。最后再给一个专业点的对于linux下进程间通信的几种主要手段的介绍：
>1、管道（Pipe）及有名管道（named pipe）：管道可用于具有亲缘关系进程间的通信，有名管道克服了管道没有名字的限制，因此，除具有管道所具有的功能外，它还允许无亲缘关系进程间的通信；

>2、信号（Signal）：信号是比较复杂的通信方式，用于通知接受进程有某种事件发生，除了用于进程间通信外，进程还可以发送信号给进程本身；linux除了支持Unix早期信号语义函数sigal外，还支持语义符合Posix.1标准的信号函数sigaction（实际上，该函数是基于BSD的，BSD为了实现可靠信号机制，又能够统一对外接口，用sigaction函数重新实现了signal函数）；

>3、报文（Message）队列（消息队列）：消息队列是消息的链接表，包括Posix消息队列system V消息队列。有足够权限的进程可以向队列中添加消息，被赋予读权限的进程则可以读走队列中的消息。消息队列克服了信号承载信息量少，管道只能承载无格式字节流以及缓冲区大小受限等缺点。

>4、共享内存：使得多个进程可以访问同一块内存空间，是最快的可用IPC形式。是针对其他通信机制运行效率较低而设计的。往往与其它通信机制，如信号量结合使用，来达到进程间的同步及互斥。

>5、信号量（semaphore）：主要作为进程间以及同一进程不同线程之间的同步手段。

>6、套接口（Socket）：更为一般的进程间通信机制，可用于不同机器之间的进程间通信。起初是由Unix系统的BSD分支开发出来的，但现在一般可以移植到其它类Unix系统上：Linux和SystemV的变种都支持套接字。

这里仍然留下几个遗留问题：  
1、对这几种进程间通信手段的比较。  
2、在web技术上有哪些好用的进程间通信方法。


