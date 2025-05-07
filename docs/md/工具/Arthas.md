### 查看方法列表

```shell
sm com.yang.***Controller
```



### 基于Arthas进行问题诊断

#### 定位cpu100%

无Arthas之前：

- top

- jps定位java进程号

- 找到最耗时的线程号

- 线程号转16进制

- jstack导出日志，全局搜索16进制线程号定位到代码片段

有Arthas之后：

thread，观察占用cpu高的线程

thread [线程序号]，直接定位问题

jad --source-only [类全限定名]  ，查看源码

#### 定位死锁问题

thread -b

#### 运行耗时问题

trace [类名] [方法名]

#### 方法耗时统计

比如希望每5s查看某个方法的情况

moitor -c 5 [类名] [方法名]

#### 定位出入参错误问题

```shell
watch com.example.arthasExample.UserServiceImpl mysql '{params[0],returnObj}'
```

#### 监控方法调用路径

stack [类名] [方法名]

#### 内存溢出问题

heapdump  [path:D://heap.hprof]



### 进阶操作

#### 线上替换代码

1、将生产环境的字节码反编译到本地

```shell
jad  --source-only com.example.arthasExample.TestController >  d://TestController.java
```

2、修改代码片段

3、**找到这个类对应类加载器的hash码**

```
sc -d *TestController | grep classLoaderHash
```

4、基于这个类加载器将修改后的java文件编译成字节码文件

```
 mc -c 18b4aac2 d://TestController.java -d d://
```

5、最后将代码热更新到正在运行的程序

```
redefine d://com/example/arthasExample/TestController.class
```



#### 

#### Arthas统计方法耗时watch指令原理

```
watch com.example.MyService sayHello "{params, result, throwExp} -> { return 'Exception: ' + throwExp; }" -E
```

![](..\..\images\Arthas\Arthas的watch命令.png)

基于这个类的全路径定位到类的全路径，并对该类型通过**字节码桩技术**对类的方法进行增强，即插入一段代码进行在方法执行前后插入时间，实现耗时统计