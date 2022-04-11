# 简介

java.util.concurrent(J.U.C)

- atomic 原子类框架
- locks 锁框架
- sync 同步器框架
- collections 集合框架
- executors 执行器框架

# 原子操作

CAS(compare and swap)原子操作的一种，可用于在多线程编程中实现不被打断的数据交换操作，从而避免多线程同时改写某一数据时由于执行顺序不确定性以及中断的不可预知性产生的数据不一致问题。
该操作通过将内存中的值与指定数据进行比较，当数值一样时将内存中的数据替换为新的值。

## 原子值操作 AtomicInteger/AtomicLong/AtomicReference

```java
import java.util.concurrent.atomic.AtomicInteger;

public class Atomic {
    public static void main(String[] args) {
        AtomicInteger atomicInteger = new AtomicInteger();

        // set
        atomicInteger.set(1);

        // get getAnd*
        System.out.println(atomicInteger.getAndSet(1));
        System.out.println(atomicInteger.getAndAdd(1));
        System.out.println(atomicInteger.getAndIncrement());
        System.out.println(atomicInteger.getAndDecrement());
        System.out.println(atomicInteger.getAndUpdate(i -> 1));
        System.out.println(atomicInteger.get());
        System.out.println(atomicInteger.getAcquire());
        System.out.println(atomicInteger.getPlain());
        System.out.println(atomicInteger.getOpaque());

        // compareAnd**
        atomicInteger.set(1);
        System.out.println(atomicInteger.compareAndSet(1, 2));
        System.out.println(atomicInteger.compareAndExchange(2, 3));
    }
}
```

## 原子操作值数组 AtomicIntegerArray/AtomicLongArray/AtomicReferenceArray

```java
import java.util.concurrent.atomic.AtomicIntegerArray;

public class Atomic {
    public static void main(String[] args) {
        AtomicIntegerArray atomicIntegerArray = new AtomicIntegerArray(10);
        int index = 0;

        // set
        atomicIntegerArray.set(0, 1);

        // get getAnd*
        System.out.println(atomicIntegerArray.getAndSet(index, 1));
        System.out.println(atomicIntegerArray.getAndAdd(index, 1));
        System.out.println(atomicIntegerArray.getAndIncrement(index));
        System.out.println(atomicIntegerArray.getAndDecrement(index));
        System.out.println(atomicIntegerArray.getAndUpdate(index, i -> 1));
        System.out.println(atomicIntegerArray.get(index));
        System.out.println(atomicIntegerArray.getAcquire(index));
        System.out.println(atomicIntegerArray.getPlain(index));
        System.out.println(atomicIntegerArray.getOpaque(index));

        // compareAnd**
        atomicIntegerArray.set(index, 1);
        System.out.println(atomicIntegerArray.compareAndSet(index, 1, 2));
        System.out.println(atomicIntegerArray.compareAndExchange(index, 2, 3));
    }
}
```

## 原子操作对象字段 AtomicIntegerFieldUpdater/AtomicLongFieldUpdater/AtomicReferenceFieldUpdater

```java
import java.util.concurrent.atomic.AtomicIntegerFieldUpdater;

public class Atomic {
    private volatile int i;

    public static void main(String[] args) {
        AtomicIntegerFieldUpdater<Atomic> atomicIntegerFieldUpdater = AtomicIntegerFieldUpdater.newUpdater(Atomic.class, "i");
        Atomic atomic = new Atomic();

        // set
        atomicIntegerFieldUpdater.set(atomic, 1);

        // get getAnd*
        System.out.println(atomicIntegerFieldUpdater.getAndSet(atomic, 1));
        System.out.println(atomicIntegerFieldUpdater.getAndAdd(atomic, 1));
        System.out.println(atomicIntegerFieldUpdater.getAndIncrement(atomic));
        System.out.println(atomicIntegerFieldUpdater.getAndDecrement(atomic));
        System.out.println(atomicIntegerFieldUpdater.getAndUpdate(atomic, i -> 1));
        System.out.println(atomicIntegerFieldUpdater.get(atomic));

        // compareAnd**
        atomicIntegerFieldUpdater.set(atomic, 1);
        System.out.println(atomicIntegerFieldUpdater.compareAndSet(atomic, 1, 2));
    }
}
```

## 累加器 LongAccumulator/DoubleAccumulator

```java
import java.util.concurrent.atomic.LongAccumulator;

public class Atomic {
    public static void main(String[] args) {
        LongAccumulator longAccumulator = new LongAccumulator((left, right) -> left * right, 2);

        // accumulate
        longAccumulator.accumulate(2);

        // get
        System.out.println(longAccumulator.get());

        // reset/getAndReset
        longAccumulator.reset();
        System.out.println(longAccumulator.getThenReset());
    }
}
```

# locks 锁框架

## 公平策略/非公平策略

公平策略 / 非公平策略的功能，默认为非公平策略。

- 公平策略：在多个线程争用锁的情况下，公平策略倾向于将访问权授予等待时间最长的线程。也就是说，相当于有一个线程等待队列，先进入等待队列的线程后续会先获得锁，这样按照“先来后到”的原则，对于每一个等待线程都是公平的。
- 非公平策略：在多个线程争用锁的情况下，能够最终获得锁的线程是随机的（由底层OS调度）。

## Lock 接口

```java
public interface Lock {
    void lock();

    void lockInterruptibly() throws InterruptedException;

    boolean tryLock();

    boolean tryLock(long time, TimeUnit unit) throws InterruptedException;

    Condition newCondition();
}
```

## Condition 条件变量接口

```java
public interface Condition {
    void await() throws InterruptedException;

    void awaitUninterruptibly();

    long awaitNanos(long nanosTimeout) throws InterruptedException;

    boolean await(long time, TimeUnit unit) throws InterruptedException;

    boolean awaitUntil(Date deadline) throws InterruptedException;

    void signal();

    void signalAll();
}
```

## Lock Condition 使用

```java

import java.time.LocalTime;
import java.util.concurrent.locks.Condition;
import java.util.concurrent.locks.Lock;
import java.util.concurrent.locks.ReentrantLock;

public class JUCLock {
    public static void main(String[] args) throws InterruptedException {
        BoundedBuffer buffer = new BoundedBuffer();

        new Thread(() -> {
            while (true) {
                try {
                    Thread.sleep(1000);
                    int i = LocalTime.now().toSecondOfDay();
                    System.out.println("put: " + i);
                    buffer.put(i);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        }).start();

        while (true) {
            try {
                Thread.sleep(2000);
                System.out.println("take: " + buffer.take());
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }
}

class BoundedBuffer {
    private final Lock lock = new ReentrantLock();
    private final Condition notEmpty = lock.newCondition();
    private final Condition notFull = lock.newCondition();

    private final Integer[] array = new Integer[10];
    private int size = 0;

    public void put(int i) throws InterruptedException {
        try {
            lock.lock();

            while (size == array.length)
                notFull.await();

            array[size] = i;
            size++;

            notEmpty.signal();
        } finally {
            lock.unlock();
        }
    }

    public int take() throws InterruptedException {
        try {
            lock.lock();

            while (size == 0)
                notEmpty.await();

            size--;
            Integer integer = array[size];

            notFull.signal();

            return integer;
        } finally {
            lock.unlock();
        }
    }
}
```

## ReadWriteLock 读写锁

读锁可以由多个线程持有，写锁只能有一个线程持有

```java
public interface ReadWriteLock {
    Lock readLock();

    Lock writeLock();
}
```

## ReentrantLock 可重入锁

持有锁的线程可以再次请求锁

```java
import java.util.concurrent.locks.ReentrantLock;

public class JUCReentrantLock {
    public static void main(String[] args) throws InterruptedException {
        ReentrantLock lock = new ReentrantLock();

        lock.lock();
        System.out.println("lock count: " + lock.getHoldCount());
        lock.lock();
        System.out.println("lock count: " + lock.getHoldCount());

        lock.unlock();
        System.out.println("lock count: " + lock.getHoldCount());
        lock.unlock();
        System.out.println("lock count: " + lock.getHoldCount());
    }
}
```

## ReentrantReadWriteLock 可重入读写锁

## LockSupport

LockSupport类使用了一种名为Permit（许可）的概念来做到阻塞和唤醒线程的功能，可以把许可看成是一种(0,1)信号量（Semaphore），但与 Semaphore 不同的是，许可的累加上限是1。
初始时，permit为0，当调用unpark()方法时，线程的permit加1，当调用park()方法时，如果permit为0，则调用线程进入阻塞状态。

```java
import java.util.concurrent.locks.LockSupport;

public class JUCLock {
    public static void main(String[] args) {
        Thread mainThread = Thread.currentThread();

        System.out.println("unpark by" + Thread.currentThread());
        LockSupport.unpark(mainThread);
        System.out.println("start park");
        LockSupport.park();
        System.out.println("end park");

        new Thread(() -> {
            try {
                Thread.sleep(1000);
                System.out.println("unpark by" + Thread.currentThread());
                LockSupport.unpark(mainThread);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }).start();

        System.out.println("start park");
        LockSupport.park();
        System.out.println("end park");
    }
}
```

## AQS 锁机制

Java中的大部分同步类（Lock、Semaphore、ReentrantLock等）都是基于 AbstractQueuedSynchronizer（简称为AQS）实现的。
AQS是一种提供了原子式管理同步状态、阻塞和唤醒线程功能以及队列模型的简单框架。 AQS底层依赖CAS和同步队列。

AQS框架，分离了构建同步器时的一系列关注点，它的所有操作都围绕着资源——同步状态（synchronization state）来展开，并替用户解决了如下问题：

- 资源是可以被同时访问？还是在同一时间只能被一个线程访问？（共享/独占功能）
- 访问资源的线程如何进行并发管理？（等待队列）
- 如果线程等不及资源了，如何从等待队列退出？（超时/中断）

### AQS 模板模式

AQS是一个抽象类，当构建一个同步组件的时候，需要定义一个子类继承AQS，应用了模板方法设计模式。 模板模式由一个抽象类和一个实现类组成，抽象类中主要有三类方法：

- 模板方法：实现了算法主体框架，供外部调用。里面会调用原语操作和钩子操作。
- 原语操作：即定义的抽象方法，子类必须重写。
- 钩子操作：和原语操作类似，也是供子类重写的， 区别是钩子操作子类可以选择重写也可以选择不重写，如果不重写则使用抽象类默认操作，通常是一个空操作或抛出异常。

#### AQS 钩子操作

- protected boolean tryAcquire(int arg); 独占式获取同步状态，成功返回true，失败返回false
- protected boolean tryRelease(int arg); 独占式释放同步状态，成功返回true，失败返回false
- protected int tryAcquireShared(int arg); 共享式获取同步状态，获取成功则返回值>=0
- protected boolean tryReleaseShared(int arg); 共享式释放同步状态，成功返回true，失败返回false
- protected boolean isHeldExclusively(); 判断同步器是否在独占模式下被占用，一般用来表示同步器是否被当前线程占用

#### 支持中断、超时

- 阻塞和非阻塞（例如tryLock）同步；
- 可选的超时设置，让调用者可以放弃等待；
- 可中断的阻塞操作。

#### 支持独占模式和共享模式

#### 支持Condition条件等待

#### 支持Condition条件等待

AQS框架内部通过一个内部类ConditionObject，实现了Condition接口，以此来为子类提供条件等待的功能。

#### Skeleton Method

AQS利用了模板方法模式，其中大多数方法都是final或是private的，我们把这类方法称为Skeleton Method，也就是说这些方法是AQS框架自身定义好的骨架，子类是不能覆写的。
下面会按类别简述一些比较重要的方法，具体实现细节及原理会在本系列后续部分详细阐述。

##### CAS操作

- compareAndSetState protected final CAS修改同步状态值
- compareAndSetHead private final CAS修改等待队列的头指针
- compareAndSetTail private final CAS修改等待队列的尾指针
- compareAndSetWaitStatus private static final CAS修改结点的等待状态
- compareAndSetNext private static final CAS修改结点的next指针

##### 等待队列的核心操作

- enq private 入队操作
- addWaiter private 入队操作
- setHead private 设置头结点
- unparkSuccessor private 唤醒后继结点
- doReleaseShared private 释放共享结点
- setHeadAndPropagate private 设置头结点并传播唤醒

##### 资源的获取操作

- cancelAcquire private 取消获取资源
- shouldParkAfterFailedAcquire private static 判断是否阻塞当前调用线程
- acquireQueued final 尝试获取资源,获取失败尝试阻塞线程
- doAcquireInterruptibly private 独占地获取资源（响应中断）
- doAcquireNanos private 独占地获取资源（限时等待）
- doAcquireShared private 共享地获取资源
- doAcquireSharedInterruptibly private 共享地获取资源（响应中断）
- doAcquireSharedNanos private 共享地获取资源（限时等待）

- acquire public final 独占地获取资源
- acquireInterruptibly public final 独占地获取资源（响应中断）
- acquireInterruptibly public final 独占地获取资源（限时等待）
- acquireShared public final 共享地获取资源
- acquireSharedInterruptibly public final 共享地获取资源（响应中断）
- tryAcquireSharedNanos public final 共享地获取资源（限时等待）

##### 资源的释放操作

- release public final 释放独占资源
- releaseShared public final 释放共享资源

#### 同步状态

同步状态，其实就是资源。AQS使用单个int（32位）来保存同步状态，并暴露出getState、setState以及compareAndSetState操作来读取和更新这个状态。

#### 线程的阻塞/唤醒

使用 LockSupport 工具类实现阻塞/唤醒

#### 等待队列

等待队列，是AQS框架的核心，整个框架的关键其实就是如何在并发状态下管理被阻塞的线程。 等待队列是严格的FIFO队列，是Craig，Landin和Hagersten锁（CLH锁）的一种变种，采用双向链表实现，因此也叫CLH队列。

# 辅助同步类

## CountDownLatch

倒数计数器，构造时设定计数值，当计数值归零后，所有阻塞线程恢复执行；其内部实现了AQS框架

```java
import java.util.concurrent.CountDownLatch;

public class JUCSync {
    public static void main(String[] args) throws InterruptedException {
        CountDownLatch countDownLatch = new CountDownLatch(10);

        new Thread(() -> {
            while (countDownLatch.getCount() > 0) {
                try {
                    Thread.sleep(1000);
                    System.out.println("before count down, " + countDownLatch);
                    countDownLatch.countDown();
                    System.out.println("after count down, " + countDownLatch);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        }).start();

        System.out.println("before await, " + countDownLatch);
        countDownLatch.await();
        System.out.println("after await, " + countDownLatch);
    }
}
```

## CyclicBarrier

循环栅栏，构造时设定等待线程数，当所有线程都到达栅栏后，栅栏放行；其内部通过ReentrantLock和Condition实现同步

```java
import java.util.Random;
import java.util.concurrent.BrokenBarrierException;
import java.util.concurrent.CyclicBarrier;

public class JUCSync {
    public static void main(String[] args) throws InterruptedException {
        CyclicBarrier cyclicBarrier = new CyclicBarrier(10);
        Random random = new Random();

        for (int i = 0; i < 10; i++) {
            new Thread(() -> {
                try {
                    Thread.sleep(random.nextInt(1000));
                    System.out.println(Thread.currentThread() + "is wait");
                    cyclicBarrier.await();
                    System.out.println(Thread.currentThread() + "is running");
                } catch (InterruptedException | BrokenBarrierException e) {
                    e.printStackTrace();
                }
            }).start();
        }
    }
}
```

## Semaphore

Semaphore维护了一个许可集，其实就是一定数量的“许可证”。 当有线程想要访问共享资源时，需要先获取(acquire)的许可；如果许可不够了，线程需要一直等待，直到许可可用。当线程使用完共享资源后，可以归还(release)
许可，以供其它需要的线程使用。

```java
import java.util.Random;
import java.util.concurrent.Semaphore;

public class JUCSync {
    public static void main(String[] args) {
        Random random = new Random();
        Semaphore semaphore = new Semaphore(10);
        for (int i = 0; i < 100; i++) {
            new Thread(() -> {
                try {
                    semaphore.acquire();
                    System.out.println(Thread.currentThread() + " acquire " + semaphore);
                    Thread.sleep(random.nextInt(1000));
                    semaphore.release();
                    System.out.println(Thread.currentThread() + " release " + semaphore);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }).start();
        }
    }
}
```

## Exchanger

Thread1线程到达栅栏后，会首先观察有没其它线程已经到达栅栏，如果没有就会等待，如果已经有其它线程（Thread2）已经到达了，就会以成对的方式交换各自携带的信息，因此Exchanger非常适合用于两个线程之间的数据交换。

```java
import java.util.Random;
import java.util.concurrent.Exchanger;

public class JUCSync {
    public static void main(String[] args) {
        Random random = new Random();
        Exchanger<Thread> exchanger = new Exchanger<>();

        for (int i = 0; i < 100; i++) {
            new Thread(() -> {
                try {
                    Thread.sleep(random.nextInt(1000));
                    Thread exchange = exchanger.exchange(Thread.currentThread());
                    System.out.println(Thread.currentThread() + " exchange valur with " + exchange);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }).start();
        }
    }
}
```

## Phaser

多阶段栅栏，相当于CyclicBarrier的升级版，可用于分阶段任务的并发控制执行；其内部比较复杂，支持树形结构，以减少并发带来的竞争

# 并发集合

# executors

## Executor

提交普通的可执行任务

```java
public interface Executor {
    void execute(Runnable command);
}
```

## ExecutorService

供对线程池生命周期的管理、异步任务的支持

```java
public interface ExecutorService extends Executor {
    void shutdown();

    List<Runnable> shutdownNow();

    boolean isShutdown();

    boolean isTerminated();

    boolean awaitTermination(long timeout, TimeUnit unit) throws InterruptedException;

    <T> Future<T> submit(Callable<T> task);

    <T> Future<T> submit(Runnable task, T result);

    Future<?> submit(Runnable task);

    <T> List<Future<T>> invokeAll(Collection<? extends Callable<T>> tasks) throws InterruptedException;

    <T> List<Future<T>> invokeAll(Collection<? extends Callable<T>> tasks, long timeout, TimeUnit unit) throws InterruptedException;

    <T> T invokeAny(Collection<? extends Callable<T>> tasks) throws InterruptedException, ExecutionException;

    <T> T invokeAny(Collection<? extends Callable<T>> tasks, long timeout, TimeUnit unit) throws InterruptedException, ExecutionException, TimeoutException;
}
```

## ScheduledExecutorService

提供对任务的周期性执行支持

```java
public interface ScheduledExecutorService extends ExecutorService {
    ScheduledFuture<?> schedule(Runnable command, long delay, TimeUnit unit);

    <V> ScheduledFuture<V> schedule(Callable<V> callable, long delay, TimeUnit unit);

    ScheduledFuture<?> scheduleAtFixedRate(Runnable command, long initialDelay, long period, TimeUnit unit);

    ScheduledFuture<?> scheduleWithFixedDelay(Runnable command, long initialDelay, long delay, TimeUnit unit);
}
```

## Executors

Executor 工厂类

```java
import java.util.concurrent.Executors;

public class JUCLock {
    public static void main(String[] args) {
        // 单线程池
        Executors.newSingleThreadExecutor();
        // 单调度线程池
        Executors.newSingleThreadScheduledExecutor();

        // 大小不固定的线程池
        Executors.newCachedThreadPool();

        // 大小固定的调度线程池
        Executors.newScheduledThreadPool(10);
    }
}
```

## Future