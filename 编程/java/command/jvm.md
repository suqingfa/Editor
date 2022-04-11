# jps

显示JAVA进程

java进程启动时会在 /tmp/hsperfdata_${USER} 目录下创建对应进程ID的文件，jps通过读取文件显示进程状态

```shell
jps [-q] [-mlvV] [<hostid>]
```

- -q 仅显示进程ID
- -m 输出main函数传入的参数
- -l 输出应用程序主类完整package名称或jar完整名称
- -v 列出jvm参数
- -V 输出通过flag文件传递到JVM中的参数

# jinfo

显示java进程的环境变量、命令参数等, 也可以调整各项参数

# jstack

显示java进程里的线程信息

```shell
jstack [-l][-e] <pid>
```

- -l 显示线程持有的锁
- -e 显示线程扩展信息

# jstat

对java进程的资源和性能进行实时的命令行的监控，包括了classloader，compiler，gc等

# jmap

显示java进程的物理内在占用

# jconsole

基于JMX的实时图形化监测工具