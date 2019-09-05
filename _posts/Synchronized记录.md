# Synchronized的实现原理和JVM的优化
## 使用方式
- 普通方法加锁，即给当前实例加锁
- 静态方法加锁，给当前类的Class对象加锁
- 同步代码块
### 实现原理
同步代码块，使用monitor实现同步，在开始的地方插入monitorenter指令，在结束的释放使用monitorexit。

对于修饰方法，则是在方法的acces_flags字段中做上同步标记。

### 优化的原因
在jdk6以前，使用synchronized进行同步，底层是调用系统的mutex锁，这就涉及操作系统中用户态和内核态的切换，需要消耗处理器时间。因此会把synchronized称之为重量级锁，在jdk6以前常用ReentrantLock

### 优化措施
- 自旋锁
- 轻量级锁
- 偏向锁
- 重量级锁

Java的虚拟机编译的时候，会进行判断，自动进行锁消除或者锁粗化（将几个能够合在一起的同步块合并为同一个代码块）。以上这几种锁会填充在Java的对象头中markword字段，通过markword字段中信息就可以知道对象的具体锁的状态。

锁升级，锁优化和锁粗化具体细节可以详细查看参看文章，很具体。

参考文章：[Synchronized底层优化（偏向锁、轻量级锁）](https://www.cnblogs.com/paddix/p/5405678.html)