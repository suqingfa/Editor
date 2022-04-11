# 性能指标
响应时间
吞吐量
并发数
内存占用

# 性能监控
cpu load 过高
程序响应时间长
死锁
GC频繁
内存泄露
OOM

## jps 
src/jdk.jcmd/share/classes/sun/tools/jps/Jps.java
list the instrumented JVMs on the target system
显示指定系统中虚拟机进程

-XX:-UsePerfData 禁用UsePerfData参数后，jps不显示java虚拟机

## jstat
src/jdk.jcmd/share/classes/sun/tools/jstat/Jstat.java
monitor JVM statistics
查看jvm统计信息 通过读取 /tmp/hsperfdata_${USER}/{pid} 实现

jstat outputOptions [-t] [-h lines] vmid [interval [count]]

-t JVM启动总时间
-h lines 显示lines行后显示表头
interval 统计时间间隔
count 统计次数

outputOptions
- options 显示所有命令
- class 显示加载类相关信息
- compiler 显示JIT相关信息
- gc GC堆统计
- gccapacity 
- gccause 引起GC的原因
- gcnew 新生代统计
- gcnewcapacity 
- gcold 老年代统计
- gcoldcapacity
- gcmetaspacecapacity 
- gcutil 
- printcomilation 显示JIT编译的方法

## jinfo
src/jdk.jcmd/share/classes/sun/tools/jinfo/JInfo.java
generate Java configuration information for a specified Java process
查看和修改JVM配置参数

jinfo [option] pid

option
- flag name 显示参数name的值
- flag [+|-]name 设置参数
- flag name=value 设置参数
- flags 显示JVM参数
- sysprops 显示System.getProperties()的所有值

## jmap
src/jdk.jcmd/share/classes/sun/tools/jmap/JMap.java
print details of a specified process
导出内存映射和内存使用情况

jmap [options] pid

option
- clstats 显示堆中类加载信息
- finalizerinfo 等待finalizer的实例的信息
- histo[:live] 堆中备类的统计信息
- dump:dump_options 导出内存快照
	- live 仅导出存活的类
	- format=b 导出文件格式
	- file=filename 导出文件名

## jstack
src/jdk.jcmd/share/classes/sun/tools/jstack/JStack.java
print Java stack traces of Java threads for a specified Java process
显示栈信息

jstack [options] pid

options
- l 

## jcmd
src/jdk.jcmd/share/classes/sun/tools/jcmd/JCmd.java
send diagnostic command requests to a running Java Virtual Machine (JVM)

## jconsole
src/jdk.jconsole/share/classes/sun/tools/jconsole/JConsole.java
start a graphical console to monitor and manage Java applications

jconsole [-interval=n] [-notile] [-plugin path] [-version] [connection ... ] [-Jinput_arguments]

## jhsdb

# 性能分析

# 性能优化 
