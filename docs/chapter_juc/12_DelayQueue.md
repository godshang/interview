# DelayQueue 源码分析

DelayQueue是一个无界的BlockingQueue，其特化的参数是Delayed。Delayed扩展了Comparable接口，比较的基准为延时的时间值，Delayed接口的实现类getDelay的返回值应为固定值（final）。DelayQueue内部是使用PriorityQueue实现的。

```
DelayQueue = BlockingQueue + PriorityQueue + Delayed
```

DelayQueue的关键元素BlockingQueue、PriorityQueue、Delayed。可以这么说，DelayQueue是一个使用优先队列（PriorityQueue）实现的BlockingQueue，优先队列的比较基准值是时间。

注意：DelayQueue中不允许添加null元素。由于DelayQueue是一个无界队列，使用时要注意避免资源耗尽的情况。

## 入队方法

入队方法有add、offer、put方法，add和put方法均通过调用offer方法实现。因为是一个无界队列，所以put方法永远不会阻塞。

```java
public boolean add(E e) {
    return offer(e);
}

public void put(E e) {
    offer(e);
}

public boolean offer(E e) {
    final ReentrantLock lock = this.lock;
    lock.lock();
    try {
        E first = q.peek();
        q.offer(e);
        if (first == null || e.compareTo(first) < 0)
            available.signalAll();
        return true;
    } finally {
        lock.unlock();
    }
}
```

offer方法向内部的PriorityQueue添加元素，同时会检查队列头部的元素是否到期。

## 出队方法

出队方法有remove、poll、take方法。

remove方法直接从队列中移除一个元素。poll方法会判断队首的元素是否到期，如果还未到期会返回空，否则返回队首元素；如果队列不为空，则会通知其他线程来获取元素。take方法在获取不到元素时会一直阻塞。

```java
public boolean remove(Object o) {
    final ReentrantLock lock = this.lock;
    lock.lock();
    try {
        return q.remove(o);
    } finally {
        lock.unlock();
    }
}

public E poll() {
    final ReentrantLock lock = this.lock;
    lock.lock();
    try {
        E first = q.peek();
        if (first == null || first.getDelay(TimeUnit.NANOSECONDS) > 0)
            return null;
        else {
            E x = q.poll();
            assert x != null;
            if (q.size() != 0)
                available.signalAll();
            return x;
        }
    } finally {
        lock.unlock();
    }
}

public E take() throws InterruptedException {
    final ReentrantLock lock = this.lock;
    lock.lockInterruptibly();
    try {
        for (;;) {
            E first = q.peek();
            if (first == null) {
                available.await();
            } else {
                long delay =  first.getDelay(TimeUnit.NANOSECONDS);
                if (delay > 0) {
                    long tl = available.awaitNanos(delay);
                } else {
                    E x = q.poll();
                    assert x != null;
                    if (q.size() != 0)
                        available.signalAll(); // wake up other takers
                    return x;

                }
            }
        }
    } finally {
        lock.unlock();
    }
}
```