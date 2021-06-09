### GC垃圾回收器

1.判断一个类是否"无用",则需同时满足三个条件:

(1)该类所有的实例都已经被回收，也就是java堆中不存在该类的任何实例。

(2)加载该类 ClassLoader 已经被回收

(3)该类对应的java.lang.Class对象没有在任何地方被引用，无法在任何地方通过反射访问该类 的方法。

虚拟机可以对满足上述3个条件的无用类进行回收，这里说的是可以回收而不是必然回收。

 

2.可达性分析算法(Reachability Analysis)

 这是Java虚拟机采用的判定对象是否存活的算法，通过一系列的称为 Gc Roots的对象作为起始点。

![image-20210608104734631](C:\Users\caohan\AppData\Roaming\Typora\typora-user-images\image-20210608104734631.png)

JDK1.8 JVM 内存布局:

![image-20210608104931315](C:\Users\caohan\AppData\Roaming\Typora\typora-user-images\image-20210608104931315.png)