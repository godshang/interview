# ArrayBlockingQueue 源码分析

ArrayBlockingQueue的原理就是使用一个可重入锁和这个锁生成的两个条件对象进行并发控制(classic two-condition algorithm)。

ArrayBlockingQueue是一个带有长度的阻塞队列，初始化的时候必须要指定队列长度，且指定长度之后不允许进行修改。

## 构造方法

```java
public ArrayBlockingQueue(int capacity) { ... }
public ArrayBlockingQueue(int capacity, boolean fair) { ... }
public ArrayBlockingQueue(int capacity, boolean fair, Collection<? extends E> c) { ... }
```

ArrayBlockingQueue提供了三种构造方法，参数含义如下：
* capacity：容量，即队列大小。
* fair：是否公平锁。
* c：队列初始化元素，顺序按照Collection遍历顺序。

## 入队方法

```java
public void put(E e) throws InterruptedException {
    if (e == null) throw new NullPointerException();
    final E[] items = this.items;
    final ReentrantLock lock = this.lock;
    lock.lockInterruptibly();
    try {
        try {
            while (count == items.length)
                notFull.await();
        } catch (InterruptedException ie) {
            notFull.signal(); // propagate to non-interrupted thread
            throw ie;
        }
        insert(e);
    } finally {
        lock.unlock();
    }
}

private void insert(E x) {
    items[putIndex] = x;
    putIndex = inc(putIndex);
    ++count;
    notEmpty.signal();
}
```

生产者首先获得锁lock，然后判断队列是否已经满了，如果满了，则等待，直到被唤醒，然后调用insert插入元素。

而对于非阻塞的方法来说，并没有使用到条件对象，而是直接根据内部容器是否已满，来决定是否插入新元素。

```java
public boolean offer(E e) {
    if (e == null) throw new NullPointerException();
    final ReentrantLock lock = this.lock;
    lock.lock();
    try {
        if (count == items.length)
            return false;
        else {
            insert(e);
            return true;
        }
    } finally {
        lock.unlock();
    }
}

public boolean add(E e) {
    if (offer(e))
        return true;
    else
        throw new IllegalStateException("Queue full");
}
```

## 出队方法

```java
public E take() throws InterruptedException {
    final ReentrantLock lock = this.lock;
    lock.lockInterruptibly();
    try {
        try {
            while (count == 0)
                notEmpty.await();
        } catch (InterruptedException ie) {
            notEmpty.signal(); // propagate to non-interrupted thread
            throw ie;
        }
        E x = extract();
        return x;
    } finally {
        lock.unlock();
    }
}

private E extract() {
    final E[] items = this.items;
    E x = items[takeIndex];
    items[takeIndex] = null;
    takeIndex = inc(takeIndex);
    --count;
    notFull.signal();
    return x;
}
```

消费者首先获得锁，然后判断队列是否为空，为空，则等待，直到被唤醒，然后调用extract获取元素。

同插入类似，非阻塞版本的方法未使用条件对象：

```java
public E poll() {
    final ReentrantLock lock = this.lock;
    lock.lock();
    try {
        if (count == 0)
            return null;
        E x = extract();
        return x;
    } finally {
        lock.unlock();
    }
}

public E remove() {
    E x = poll();
    if (x != null)
        return x;
    else
        throw new NoSuchElementException();
}
```
