# GC垃圾回收器

JDK7/8之后，HotSpot虚拟机所有的收集器和组合

<img src="https://raw.githubusercontent.com/RebornL/picbed/master/20190903154641.png" style="zoom:150%;" />



## Minor GC和Full GC

- Minor GC：新生代GC，发生在新生代的垃圾收集动作，比较频繁，速度也会比较快
- Full GC：老年代GC，速度比Minor GC慢十倍以上，一般发生不会那么频繁



## GC种类

- 新生代收集器：Serial、ParNew、Parallel Scavenge

- 老年代收集器：CMS、Serial Old（MSC）、Parallel Old
- 整堆收集器：G1



### Serial收集器

- 单线程，开销小，速度快（一两百兆停顿时间为100多毫秒）
- **client下默认新生代收集器**
- 垃圾回收算法采用**复制算法**

<img src="https://raw.githubusercontent.com/RebornL/picbed/master/20190903155452.png" style="zoom:150%;" />



### ParNew收集器

- 多线程（是Serial的多线程版）
- Server场景下默认的新生代收集器
- 可以与CMS进行配合（这个涉及到历史原因，实现的代码框架不一样）



### Parallel Scavenge收集器

- 多线程
- 以吞吐量优先，尽可能地缩短垃圾收集时用户线程的停顿时间
- 新生代收集器
- 复制算法



### Serial Old收集器

- 单线程
- Client场景下**老年代收集器**
- 标记-整理算法

<img src="https://raw.githubusercontent.com/RebornL/picbed/master/20190903160119.png" style="zoom:150%;" />



### Parallel Old收集器

- 多线程
- Server场景下**老年代收集器**，与Parallel Scavenge配合使用，注重**吞吐量和CPU资源敏感**的场景
- **标记-整理**算法

<img src="https://raw.githubusercontent.com/RebornL/picbed/master/20190903160323.png" style="zoom:150%;" />



### CMS(Concurrent Mark Sweep)收集器

- 标记-清除算法
- 以获取最短回收停顿时间为目标，并发收集，低停顿
- Client场景老年代收集器，注重服务的响应速度
- 需要更多内存
- 第一次实现垃圾收集线程和用户线程同时工作



CMS收集分四个步骤：

1. 初始标记：Stop the world，但时间短，速度快
2. 并发标记：耗时较长，应用程序也在运行，*不能保证标记出所有存活的对象*
3. 重新标记：Stop the world（但比并发标记停顿的时间短），只是修正上一步做出的标记
4. 并发清楚：回收垃圾对象，用户进程并发执行

（从步骤中可以看到CMS无法处理**浮动垃圾**）

<img src="https://raw.githubusercontent.com/RebornL/picbed/master/20190903161313.png" style="zoom:150%;" />

### G1收集器

- 充分利用多CPU、多核环境下的硬件优势
- 利用并行缩短停顿时间
- 垃圾回收和用户程序并发执行
- 独立管理整个堆（堆划分成多个独立Region）

<img src="https://raw.githubusercontent.com/RebornL/picbed/master/20190903162856.png" style="zoom:150%;" />



参考文章链接：

- [JVM的7种垃圾收集器：主要特点 应用场景 设置参数 基本运行原理](https://www.cnblogs.com/alsf/p/9484770.html)
- [JVM分析02篇-GC算法和种类](https://zhuanlan.zhihu.com/p/36596830)

