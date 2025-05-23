**常量池**

class常量池：编译器生成，只读，文件级别的数据结构，定义类的编译时的依赖和信息

运行时常量池：类加载时被解析，验证后存储在**方法区**的数据结构；存储类常量，方法和字段引用等

字符串常量池：存储字符串字面量的内存区域。java7后放入堆中，专注于字符串优化，堆中唯一字符实例来减少字符串的创建。



GC需要再安全点(safePoint)才可执行。安全点确保当前线程暂停时，程序的状态是可知和一致的。



**三色标记法**：

​	白色：尚未被检查，可能是垃圾

​	灰色：被检查过，并且可达，但其引用的对象还未全部检查

​	黑色：	对象和他引用的对象都是可达的

步骤：

​	开始都是白色，从GCROOT开始，

​	根标记为灰色，扫描灰色对象

​		灰色对象所有引用的白色都变成灰色，将灰色标记为黑色

最后消除白色垃圾



**对象分配：**

​	分配内存：在堆中分配内存空间

​	对象头：元信息，哈希，锁，垃圾回收信息

​	零值初始化：成员变量初始化为0

​	构造函数调用：

​	对象引用：new返回对象的引用，将这个引用分配给一个变量

​	垃圾回收：回收对象



双亲委派机制打破
	自定义类加载器，覆盖Classloader类的findClass方法，但是像一些像String这类的是不可以覆盖的

**GC完整回收流程：**

​	标记，清除，压缩

元空间代替永久代：永久代的大小不好确定；动态加载类时会受到永久代大小限制；永久代回收会触发FULL GC耗时操作；永久代大小不易监控

