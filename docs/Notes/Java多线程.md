## Java多线程

参考资料：http://concurrent.redspider.group/RedSpider.html

## 1、Java多线程的使用

### 1.1、继承 Thread 类

```java
public class ThreadDemo1 {
    public static class MyThread extends Thread {

        @Override
        public void run() {
            System.out.println("My Thread");
        }
        
    }
    public static void main(String[] args) {
        Thread myThread = new MyThread();
        myThread.start();
    }
}
```

> 调用 start() 方法启动线程，只能调用一次

### 1.2、实现 Runnable 接口

```java
public class ThreadDemo2 {
    public static class MyThread implements Runnable {
        @Override
        public void run() {
            System.out.println("MyThread");
        }
    }
    public static void main(String[] args) {

        new Thread(new MyThread()).start();

        // Java 8 函数式编程，可以省略MyThread类
        new Thread(() -> {
            System.out.println("Java 8 匿名内部类");
        }).start();
        
        new Thread(new Runnable(){
            @Override
            public void run() {
               System.out.println("匿名内部类");
            }
        }).start();
    }  
}
```

### 1.3、Thread 类的常用方法

- currentThread()：静态方法，返回对当前正在执行的线程对象的引用；
- start()：开始执行线程的方法，java虚拟机会调用线程内的run()方法；
- yield()：yield在英语里有放弃的意思，同样，这里的yield()指的是当前线程愿意让出对当前处理器的占用。这里需要注意的是，就算当前线程调用了yield()方法，程序在调度的时候，也还有可能继续运行这个线程的；
- sleep()：静态方法，使当前线程睡眠一段时间；
- join()：使当前线程等待另一个线程执行完毕之后再继续执行，内部调用的是Object类的wait方法实现的；

### 1.4、Callable 接口

Callable 提供有返回值的线程方法

```java
import java.util.concurrent.*;
public class CallableDemo1 implements Callable<Integer>{

    @Override
    public Integer call() throws Exception {
        Thread.sleep(1000);
        return 2;
    }
    public static void main(String[] args) throws Exception{
        ExecutorService executor = Executors.newCachedThreadPool();
        CallableDemo1 callableDemo1 = new CallableDemo1();
        Future<Integer> result = executor.submit(callableDemo1);
        System.out.println(result.get());
    }
}
```

## 2、Java 线程的状态

### 2.1、操作系统的线程状态

![](https://gitee.com/sun-qiao321/picture/raw/master/images/20210621210919.png)

### 2.2、Java 线程状态

```java
public enum State {
    NEW,
    RUNNABLE,
    BLOCKED,
    WAITING,
    TIMED_WAITING,
    TERMINATED;
}
```

#### 2.2.1、NEW

只是创建了线程而并没有调用start()方法，此时线程处于NEW状态。

#### 2.2.2、start() 方法

1. 反复调用同一个线程的start()方法是否可行？
2. 假如一个线程执行完毕（此时处于TERMINATED状态），再次调用这个线程的start()方法是否可行？

观察 start() 方法的源码

```java
   public synchronized void start() {
        /**
         * A zero status value corresponds to state "NEW".
         */
        if (threadStatus != 0)
            throw new IllegalThreadStateException();
        group.add(this);
        boolean started = false;
        try {
            start0();
            started = true;
        } finally {
            try {
                if (!started) {
                    group.threadStartFailed(this);
                }
            } catch (Throwable ignore) {
            }
        }
    }
```

其中threadStatus 的状态为0 时，表示 线程处于NEW 状态，若不为0，直接抛出异常。

> 两个问题的答案都是不可行，在调用一次start()之后，threadStatus的值会改变（threadStatus !=0），此时再次调用start()方法会抛出IllegalThreadStateException异常。
>
> 比如，threadStatus为2代表当前线程状态为TERMINATED。

#### 2.2.3、RUNNABLE

表示当前线程正在运行中。处于RUNNABLE状态的线程在Java虚拟机中运行，也有可能在等待CPU分配资源。

> Java线程的**RUNNABLE**状态包括了传统操作系统线程的**ready**和**running**两个状态的。

#### 2.2.4、BLOCKED

阻塞状态。处于BLOCKED状态的线程正等待锁的释放以进入同步区。

#### 2.2.5、WAITING

等待状态。处于等待状态的线程变成RUNNABLE状态需要其他线程唤醒。

调用如下3个方法会使线程进入等待状态：

- Object.wait()：使当前线程处于等待状态直到另一个线程唤醒它；
- Thread.join()：等待线程执行完毕，底层调用的是Object实例的wait方法；
- LockSupport.park()：除非获得调用许可，否则禁用当前线程进行线程调度。

#### 2.2.6、TIMED_WAITING

超时等待状态。线程等待一个具体的时间，时间到后会被自动唤醒。

调用如下方法会使线程进入超时等待状态：

- Thread.sleep(long millis)：使当前线程睡眠指定时间；
- Object.wait(long timeout)：线程休眠指定时间，等待期间可以通过notify()/notifyAll()唤醒；
- Thread.join(long millis)：等待当前线程最多执行millis毫秒，如果millis为0，则会一直执行；
- LockSupport.parkNanos(long nanos)： 除非获得调用许可，否则禁用当前线程进行线程调度指定时间；
- LockSupport.parkUntil(long deadline)：同上，也是禁止线程进行调度指定时间；

#### 2.2.7、TERMINATED

终止状态。此时线程已执行完毕。

### 2.3、 状态的转换

![](https://gitee.com/sun-qiao321/picture/raw/master/images/20210621212403.png)

## 3、Java 线程间通信

### 3.1、锁与同步

##### 实现A先输出 、B再输出

```java
public class ObjectLock {
    private static Object lock = new Object();
    static class ThreadA implements Runnable{
        @Override
        public void run() {
            synchronized (lock){
                for(int i = 0;i<100;i++){
                    System.out.println("Thread A "+i);
                }
            }
        }
    }
    static class ThreadB implements Runnable {
        @Override
        public void run() {
            synchronized (lock) {
                for (int i = 0; i < 100; i++) {
                    System.out.println("Thread B " + i);
                }
            }
        }
    }

    public static void main(String[] args) throws InterruptedException {
        new Thread(new ThreadA()).start();
        Thread.sleep(10);
        new Thread(new ThreadB()).start();
    }
}

```

### 3.2、等待/通知机制

##### A和B交替输出

```java
public class WaitAndNotify {
    private static Object lock = new Object();

    static class ThreadA implements Runnable {
        @Override
        public void run() {
            synchronized (lock) {
                for (int i = 0; i < 5; i++) {
                    try {
                        System.out.println("ThreadA: " + i);
                        lock.notify();
                        lock.wait();
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                }
                lock.notify();
            }
        }
    }

    static class ThreadB implements Runnable {
        @Override
        public void run() {
            synchronized (lock) {
                for (int i = 0; i < 5; i++) {
                    try {
                        System.out.println("ThreadB: " + i);
                        lock.notify();
                        lock.wait();
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                }
                lock.notify();
            }
        }
    }

    public static void main(String[] args) throws InterruptedException {
        new Thread(new ThreadA()).start();
        Thread.sleep(1000);
        new Thread(new ThreadB()).start();
    }
}

// 输出：
ThreadA: 0
ThreadB: 0
ThreadA: 1
ThreadB: 1
ThreadA: 2
ThreadB: 2
ThreadA: 3
ThreadB: 3
ThreadA: 4
ThreadB: 4
```

### 3.3、信号量

##### 实现A和B交替输出

```java
public class Signal {
    private static volatile int signal = 0;
    static class ThreadA implements Runnable{
        @Override
        public void run() {
            while(signal <10){
                if(signal % 2 == 0) {
                    System.out.println("threadA: " +signal);
                    signal++;
                }
            }
        }
    }
    static class ThreadB implements Runnable {
        @Override
        public void run() {
            while (signal < 10) {
                if (signal % 2 == 1) {
                    System.out.println("threadB: " + signal);
                    signal = signal + 1;
                }
            }
        }
    }

    public static void main(String[] args) throws InterruptedException {
        new Thread(new ThreadA()).start();
        Thread.sleep(1000);
        new Thread(new ThreadB()).start();
    }
}
```

### 3.4、管道

管道是基于“管道流”的通信方式。JDK提供了`PipedWriter`、 `PipedReader`、 `PipedOutputStream`、 `PipedInputStream`。其中，前面两个是基于字符的，后面两个是基于字节流的。





### 3.5、锁的概念

##### 死锁与产生的必要条件

死锁：是指两个或两个以上的进程（或线程）在执行过程中，因争夺资源而造成的一种互相等待的现象，若无外力作用，它们都将无法推进下去。

产生死锁的必要条件：

①互斥条件：所谓互斥就是进程在某一时间内独占资源。

②请求与保持条件：一个进程因请求资源而阻塞时，对已获得的资源保持不放。

③不剥夺条件：进程已获得资源，在未使用完之前，不能强行剥夺。

④循环等待条件：若干进程之间形成一种头尾相接的循环等待资源。

##### 活锁

任务失败或者执行者没有被阻塞，由于某些条件没有满足，导致一直重复尝试，失败，尝试，失败。

 

死锁与活锁的区别：处于活锁的实体是在不断的改变状态，所谓的“活”，而处于死锁的实体表现为等待；活锁有可能自行解开，死锁则不能。

##### 饥饿

一个或多个线程因为种种原因无法获得所需要的资源，导致一直无法执行的状态。

导致饥饿的原因：

①高优先级线程吞噬所有低优先级线程的CPU时间。

②线程被永久阻塞在一个等待进入同步块的状态，因为其他线程总是能在它之前持续地对该同步块进行访问。

③线程在等待一个本身也处于永久等待完成的对象（比如调用这个对象的wait（）方法），因为其他线程总是被持续地获得唤醒。

##### wait() 与 sleep() 的区别？

sleep()是使线程暂停执行一段时间的方法。wait()也是一种使线程暂停执行的方法。二者区别为：

- **原理不同**

 sleep()方法是Thread类的静态方法，是线程用来控制自身流程的，它会使此线程暂停执行一段时间，而把执行机会让给其他线程，等到计时时间一到，此线程会自动苏醒。而wait()方法是Object类的方法，用于线程间的通信，这个方法会使当前拥有该对象锁的进程等待，直到其他线程用调用notify()或notifyAll()时才苏醒过来，开发人员也可以给它指定一个时间使其自动醒来。

- **对锁的处理机制不同**

 由于sleep()方法的主要作用是让线程暂停一段时间，时间一到则自动恢复，不涉及线程间的通信，因此调用sleep()方法并不会释放锁。而wait()方法则不同，当调用wait()方法后，线程会释放掉它所占用的锁，从而使线程所在对象中的其他synchronized数据可被别的线程使用。

- **使用区域不同**

 wait()方法必须放在同步控制方法或者同步语句块中使用，而sleep方法则可以放在任何地方使用。



 由于sleep不会释放锁标志，容易导致死锁问题的发生，一般情况下，不推荐使用sleep()方法，而推荐使用wait()方法。

## 4、Java内存模型JMM

### 4.1、Java 运行时数据区

<img src="https://gitee.com/sun-qiao321/picture/raw/master/images/20210621222523.png" style="zoom: 67%;" />

### 4.2、Java 内存模型 JMM

<img src="https://gitee.com/sun-qiao321/picture/raw/master/images/20210621222725.png" style="zoom:50%;" />

从图中可以看出：

1. 所有的共享变量都存在主内存中。

2. 每个线程都保存了一份该线程使用到的共享变量的副本。

3. 如果线程A与线程B之间要通信的话，必须经历下面2个步骤：

   - 线程A将本地内存A中更新过的共享变量刷新到主内存中去。

   - 线程B到主内存中去读取线程A之前已经更新过的共享变量。



### 4.3、JMM 和 Java 内存区域划分的区别和联系

- 区别

  两者是不同的概念层次。JMM是抽象的，他是用来描述一组规则，通过这个规则来控制各个变量的访问方式，围绕原子性、有序性、可见性等展开的。而Java运行时内存的划分是具体的，是JVM运行Java程序时，必要的内存划分。

- 联系

  都存在私有数据区域和共享数据区域。一般来说，JMM中的主内存属于共享数据区域，他是包含了堆和方法区；同样，JMM中的本地内存属于私有数据区域，包含了程序计数器、本地方法栈、虚拟机栈。





## [ThreadLocal](https://mp.weixin.qq.com/s/DMj4wY2Z30MylAt9QJ5pUg)

主要用来做线程变量的隔离。

程序再处理用户请求的时候，通常后端服务器是有一个线程池，来一个请求就交给一个线程来处理，那为了防止多线程并发处理请求的时候发生串数据，比如AB线程分别处理







