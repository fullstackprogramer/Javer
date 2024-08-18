# Synchronized 和 Lock 在开发工作中，应该如何选择？为什么？

**案例1：**
**区别**：synchronized：在需要同步的对象中加入此控制，synchronized 可以加在方法上，也可以加在特定代码块中，括号中表示需要锁的对象。
lock：需要显示指定起始位置和终止位置，对应 unlock。一般使用 ReentrantLock 类做为锁，多个线程中必须要使用一个 ReentrantLock 类做为对象才能保证锁的生效。
synchronized:在发生异常时候会自动释放占有的锁，因此不会出现死锁。Lock:发生异常时候，不会主动释放占有的锁，必须手动 unlock 来释放锁，可能引起死锁的发生。
锁的类型：synchronized:可重入，不可中断，非公平 Lock:可重入，可判断，可公平（两者皆可）
**性能区别**：synchronized 是托管给 JVM 执行的，而 lock 是 java 写的控制锁的代码。lock 性能更高些。
synchronized 原始采用的是 CPU 悲观锁机制，即线程获得的是独占锁。Lock 用的是乐观锁方式，其机制机制就是 CAS 操作（CompareandSwap）
synchronized:少量同步；Lock:大量同步。
Lock 可以提高多个线程进行读操作的效率。（可以通过 readwritelock 实现读写分离）
在资源竞争不是很激烈的情况下，Synchronized 的性能要优于 ReetrantLock，但是在资源竞争很激烈的情况下，Synchronized 的性能会下降几十倍，但是 ReetrantLock 的性能能维持常态；
ReentrantLock 提供了多样化的同步，比如有时间限制的同步，可以被 Interrupt 的同步（synchronized 的同步是不能 Interrupt 的）等。在资源竞争不激烈的情形下，性能稍微比 synchronized 差点点。但是当同步非常激烈的时候，synchronized 的性能一下子能下降好几十倍。而 ReentrantLock 确还能维持常态。
**底层实现**
synchronized:底层使用指令码方式来控制锁的，映射成字节码指令就是增加来两个指令：monitorenter 和 monitorexit。当线程执行遇到 monitorenter 指令时会尝试获取内置锁，如果获取锁则锁计数器+1，如果没有获取锁则阻塞；当遇到 monitorexit 指令时锁计数器-1，如果计数器为 0 则释放锁。
Lock:底层是 CAS 乐观锁，依赖 AbstractQueuedSynchronizer 类，把所有的请求线程构成一个 CLH 队列。而对该队列的操作均通过 Lock-Free（CAS）操作。
案例2：
sync 重量级同步锁，可解决一般轻量级系统并发。方法级别，对象级别。内部含有大量封装。
 适合无脑使用
 Lock，用于方法区内部，有明确起止点。灵活性虽高，一般人用不好。
 lock 分布式场景使用复杂度高，一般才用队列解决问题。
 别说你们还在手写抢占锁规则，超时处理，异常时处理。自己用 lock 可以引发 10 万个为什么
案例3：
视情况而定，synchronized 是由 jvm 来管理的，加群和释放都不用操心，用起来比较省心。lock 需要自己来控制，在之前的版本中，由于 synchronized 没有进行优化，线程阻塞时会进去内核态。所以如果需要灵活控制，可以自行用 lock 实现加群和释放。后来版本对 synchronized 优化之后，其实效率已经挺高了。所以为了省事，一般直接 synchronized 就行了。还有就是公平锁和非公平锁的区别，所以根据场景和编码能力选择最好


> 原文: <https://www.yuque.com/tulingzhouyu/db22bv/ep2z1dyk5cpgte71>