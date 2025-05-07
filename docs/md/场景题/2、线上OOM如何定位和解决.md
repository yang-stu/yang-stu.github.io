# 造成OOM的原因

### 1、一次性申请的太多

更改申请对象数量：如一次性从数据库中查询太多数据

### 2、内存资源耗尽未释放

找到未释放的对象进行释放：如数据库连接等

### 3、本身资源不够

jmap -heap 查看堆信息

jamp -heap 进程pid



# OOM定位

### 1、系统已经OOM挂了

提前设置： 

```
-XX:+HeapDumpOnOutOfMemoryError -XX:HeapDumpPath=
```

系统oom后会生成dump文件，hrpof文件

结合jVisualVm载入dump文件(hrpof)

### 2、系统中还未OOM

1、在运行时导出dump文件：会造成一次full gc。STW

```
jmap -dump:format=b,file=xushu.hprof 14660(pid)
```

2、Athas

jmap -histo:live pid 就会将当前对象和占用内存打印出来



### 3、结合jvisualVm进行调试

查看最多跟业务有关对象--》找到GCRoot--->查看线程栈