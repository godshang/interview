# PriorityBlockingQueue 源码分析

PriorityBlockingQueue 是一个无界的阻塞队列，可以视作带有并发控制的PriorityQueue。

PriorityBlockingQueue中不允许添加null元素。由于PriorityBlockingQueue是一个无界队列，使用时要注意避免资源耗尽的情况。另外一点，PriorityBlockingQueue中的比较依赖于元素实现的Comparable接口，因此未实现Comparable接口的元素也无法添加到队列中，会抛出一个ClassCastException异常。

PriorityBlockingQueue在数据结构上与DelayQueue类似，都通过内部的一个PriorityQueue实现优先级队列；在实现上又与ArrayBlockingQueue类似，通过ReentrantLock与其上的一个notEmpty的Condition对象实现，不同之处在于它没有notFull的Condition对象，因为PriorityBlockingQueue是一个无界队列。

## 构造函数

PriorityBlockingQueue提供了4个构造函数，可以创建空队列、创建一个拥有初始容量的队列、从已有集合创建队列、创建一个使用自定义比较器的队列。

PriorityBlockingQueue内部实现依赖使用了PriorityQueue，而PriorityQueue中数据存储使用了数组，同ArrayList类似，添加元素时会引起数组的创建与复制，因此，如果知道队列容量的话，初始化时就创建好会比较节省资源。

```java
public PriorityBlockingQueue() {
    q = new PriorityQueue<E>();
}

public PriorityBlockingQueue(int initialCapacity) {
    q = new PriorityQueue<E>(initialCapacity, null);
}

public PriorityBlockingQueue(int initialCapacity,
                             Comparator<? super E> comparator) {
    q = new PriorityQueue<E>(initialCapacity, comparator);
}

public PriorityBlockingQueue(Collection<? extends E> c) {
    q = new PriorityQueue<E>(c);
}
```

## 内部属性

```java
private final PriorityQueue<E> q;
private final ReentrantLock lock = new ReentrantLock(true);
private final Condition notEmpty = lock.newCondition();
```

## 入队方法

入队方法有add、offer、put方法，add和put方法均通过调用offer方法实现。因为是一个无界队列，所以put方法永远不会阻塞。

```java
public boolean add(E e) {
    return offer(e);
}

public void put(E e) {
    offer(e); // never need to block
}

public boolean offer(E e) {
    final ReentrantLock lock = this.lock;
    lock.lock();
    try {
        boolean ok = q.offer(e);
        assert ok;
        notEmpty.signal();
        return true;
    } finally {
        lock.unlock();
    }
}
```

offer方法很简单，在获取锁之后，向内部的PriorityQueue实例添加元素，然后通知等待在notEmpty上的其他线程。

## 出队方法

出队方法有remove、poll、take方法。

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
        return q.poll();
    } finally {
        lock.unlock();
    }
}

public E take() throws InterruptedException {
    final ReentrantLock lock = this.lock;
    lock.lockInterruptibly();
    try {
        try {
            while (q.size() == 0)
                notEmpty.await();
        } catch (InterruptedException ie) {
            notEmpty.signal(); // propagate to non-interrupted thread
            throw ie;
        }
        E x = q.poll();
        assert x != null;
        return x;
    } finally {
        lock.unlock();
    }
}
```

方法实现的比较简单，都是从PriorityQueue中获取元素。take方法是一个阻塞方法，当队列容量为空时，阻塞在notEmpty上，等待其他线程向队列中添加元素。