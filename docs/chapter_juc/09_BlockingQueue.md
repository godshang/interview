# BlockingQueue 源码分析

阻塞队列（BlockingQueue）是 Java 5 并发新特性中的内容，阻塞队列的接口是 java.util.concurrent.BlockingQueue，它提供了两个附加操作：当队列中为空时，从队列中获取元素的操作将被阻塞；当队列满时，向队列中添加元素的操作将被阻塞。

阻塞队列常用于生产者和消费者的场景，生产者是往队列里添加元素的线程，消费者是从队列里拿元素的线程。阻塞队列就是生产者存放元素的容器。

阻塞队列提供了四种操作方法：

<table BORDER CELLPADDING=3 CELLSPACING=1>
 <tr>
   <td></td>
   <td ALIGN=CENTER><em>Throws exception</em></td>
   <td ALIGN=CENTER><em>Special value</em></td>
   <td ALIGN=CENTER><em>Blocks</em></td>
   <td ALIGN=CENTER><em>Times out</em></td>
 </tr>
 <tr>
   <td><b>Insert</b></td>
   <td>add(e)</td>
   <td>offer(e)</td>
   <td>put(e)</td>
   <td>offer(e, time, unit)</td>
 </tr>
 <tr>
   <td><b>Remove</b></td>
   <td>remove()</td>
   <td>poll()</td>
   <td>take()</td>
   <td>poll(time, unit)</td>
 </tr>
 <tr>
   <td><b>Examine</b></td>
   <td>element()</td>
   <td>peek()</td>
   <td><em>not applicable</em></td>
   <td><em>not applicable</em></td>
 </tr>
</table>

* 抛出异常：当队列满时，再向队列中插入元素，则会抛出IllegalStateException异常。当队列空时，再向队列中获取元素，则会抛出NoSuchElementException异常。
* 返回特殊值：当队列满时，向队列中添加元素，则返回false，否则返回true。当队列为空时，向队列中获取元素，则返回null，否则返回元素。
* 一直阻塞：当阻塞队列满时，如果生产者向队列中插入元素，则队列会一直阻塞当前线程，直到队列可用或响应中断退出。当阻塞队列为空时，如果消费者线程向阻塞队列中获取数据，则队列会一直阻塞当前线程，直到队列空闲或响应中断退出。
* 超时退出：当队列满时，如果生产线程向队列中添加元素，则队列会阻塞生产线程一段时间，超过指定的时间则退出返回false。当队列为空时，消费线程从队列中移除元素，则队列会阻塞一段时间，如果超过指定时间退出返回null。

JDK6提供了6个阻塞队列。分别是：

1.	ArrayBlockingQueue：是一个用数组实现的有界阻塞队列，此队列按照先进先出（FIFO）的原则对元素进行排序。支持公平锁和非公平锁。【注：每一个线程在获取锁的时候可能都会排队等待，如果在等待时间上，先获取锁的线程的请求一定先被满足，那么这个锁就是公平的。反之，这个锁就是不公平的。公平的获取锁，也就是当前等待时间最长的线程先获取锁】
2.	LinkedBlockingQueue：一个由链表结构组成的有界队列，此队列的长度为Integer.MAX_VALUE。此队列按照先进先出的顺序进行排序。
3.	PriorityBlockingQueue： 一个支持线程优先级排序的无界队列，默认自然序进行排序，也可以自定义实现compareTo()方法来指定元素排序规则，不能保证同优先级元素的顺序。
4.	DelayQueue： 一个实现PriorityBlockingQueue实现延迟获取的无界队列，在创建元素时，可以指定多久才能从队列中获取当前元素。只有延时期满后才能从队列中获取元素。（DelayQueue可以运用在以下应用场景：1.缓存系统的设计：可以用DelayQueue保存缓存元素的有效期，使用一个线程循环查询DelayQueue，一旦能从DelayQueue中获取元素时，表示缓存有效期到了。2.定时任务调度。使用DelayQueue保存当天将会执行的任务和执行时间，一旦从DelayQueue中获取到任务就开始执行，从比如TimerQueue就是使用DelayQueue实现的。）
5.	SynchronousQueue： 一个不存储元素的阻塞队列，每一个put操作必须等待take操作，否则不能添加元素。支持公平锁和非公平锁。SynchronousQueue的一个使用场景是在线程池里。Executors.newCachedThreadPool()就使用了SynchronousQueue，这个线程池根据需要（新任务到来时）创建新的线程，如果有空闲线程则会重复使用，线程空闲了60秒后会被回收。
6.	LinkedBlockingDeque： 一个由链表结构组成的双向阻塞队列。队列头部和尾部都可以添加和移除元素，多线程并发时，可以将锁的竞争最多降到一半。
