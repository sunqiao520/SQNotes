## Java多线程

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

 sleep()方法必须捕获异常，而wait()、notify()、notifyAll()不需要捕获异常。在sleep的过程中，有可能被其他对象调用它的interrupt()，产生InterruptedException异常。



 由于sleep不会释放锁标志，容易导致死锁问题的发生，一般情况下，不推荐使用sleep()方法，而推荐使用wait()方法。

##### Java内存模型JMM





##### [ThreadLocal](https://mp.weixin.qq.com/s/DMj4wY2Z30MylAt9QJ5pUg)

主要用来做线程变量的隔离。

程序再处理用户请求的时候，通常后端服务器是有一个线程池，来一个请求就交给一个线程来处理，那为了防止多线程并发处理请求的时候发生串数据，比如AB线程分别处理



##### BlockingQueue的原理与实现

**阻塞队列 (BlockingQueue)**是Java util.concurrent包下重要的数据结构，BlockingQueue提供了线程安全的队列访问方式：当阻塞队列进行插入数据时，如果队列已满，线程将会阻塞等待直到队列非满；从阻塞队列取数据时，如果队列已空，线程将会阻塞等待直到队列非空。并发包下很多高级同步类的实现都是基于BlockingQueue实现的。

*BlockingQueue 的操作方法*
BlockingQueue 具有 4 组不同的方法用于插入、移除以及对队列中的元素进行检查。如果请求的操作不能得到立即执行的话，每个方法的表现也不同。这些方法如下：

![](https://gitee.com/sun-qiao321/picture/raw/master/images/clip_image001-1618908783219.png)

**四组不同的行为方式解释：**
• 抛异常：如果试图的操作无法立即执行，抛一个异常。
• 特定值：如果试图的操作无法立即执行，返回一个特定的值(常常是 true / false)。
• 阻塞：如果试图的操作无法立即执行，该方法调用将会发生阻塞，直到能够执行。
• 超时：如果试图的操作无法立即执行，该方法调用将会发生阻塞，直到能够执行，但等待时间不会超过给定值。返回一个特定值以告知该操作是否成功(典型的是true / false)。
无法向一个 BlockingQueue 中插入 null。如果你试图插入 null，BlockingQueue 将会抛出一个 NullPointerException。
可以访问到 BlockingQueue 中的所有元素，而不仅仅是开始和结束的元素。比如说，你将一个对象放入队列之中以等待处理，但你的应用想要将其取消掉。那么你可以调用诸如 remove(o) 方法来将队列之中的特定对象进行移除。但是这么干效率并不高(译者注：基于队列的数据结构，获取除开始或结束位置的其他对象的效率不会太高)，因此你尽量不要用这一类的方法，除非你确实不得不那么做。
**BlockingQueue 的实现类**
BlockingQueue 是个接口，你需要使用它的实现之一来使用BlockingQueue，Java.util.concurrent包下具有以下 BlockingQueue 接口的实现类：
• **ArrayBlockingQueue**：ArrayBlockingQueue 是一个有界的阻塞队列，其内部实现是将对象放到一个数组里。有界也就意味着，它不能够存储无限多数量的元素。它有一个同一时间能够存储元素数量的上限。你可以在对其初始化的时候设定这个上限，但之后就无法对这个上限进行修改了(译者注：因为它是基于数组实现的，也就具有数组的特性：一旦初始化，大小就无法修改)。
• **DelayQueue**：DelayQueue 对元素进行持有直到一个特定的延迟到期。注入其中的元素必须实现 java.util.concurrent.Delayed 接口。
• **LinkedBlockingQueue**：LinkedBlockingQueue 内部以一个链式结构(链接节点)对其元素进行存储。如果需要的话，这一链式结构可以选择一个上限。如果没有定义上限，将使用 Integer.MAX_VALUE 作为上限。
• **PriorityBlockingQueue**：PriorityBlockingQueue 是一个无界的并发队列。它使用了和类 java.util.PriorityQueue 一样的排序规则。你无法向这个队列中插入 null 值。所有插入到 PriorityBlockingQueue 的元素必须实现 java.lang.Comparable 接口。因此该队列中元素的排序就取决于你自己的 Comparable 实现。
• **SynchronousQueue**：SynchronousQueue 是一个特殊的队列，它的内部同时只能够容纳单个元素。如果该队列已有一元素的话，试图向队列中插入一个新元素的线程将会阻塞，直到另一个线程将该元素从队列中抽走。同样，如果该队列为空，试图向队列中抽取一个元素的线程将会阻塞，直到另一个线程向队列中插入了一条新的元素。据此，把这个类称作一个队列显然是夸大其词了。它更多像是一个汇合点。
使用例子：
阻塞队列的最长使用的例子就是生产者消费者模式,也是各种实现生产者消费者模式方式中首选的方式。使用者不用关心什么阻塞生产，什么时候阻塞消费，使用非常方便，代码如下：

```java
package MyThread; 
import java.util.Random;
import java.util.concurrent.BlockingQueue;
import java.util.concurrent.LinkedBlockingQueue;
import java.util.concurrent.TimeUnit; 

public class BlockingQueueTest {
	  //生产者
    public static class Producer implements Runnable{
    private final BlockingQueue<Integer> blockingQueue;
    private volatile boolean flag;
    private Random random; 
    public Producer(BlockingQueue<Integer> blockingQueue) {
        this.blockingQueue = blockingQueue;
        flag=false;
        random=new Random();	 
    }
        
    public void run() {
       while(!flag){
           int info=random.nextInt(100);
           try {
	          blockingQueue.put(info);
	          System.out.println(Thread.currentThread().getName()+" produce "+info);
	          Thread.sleep(50);
	        } catch (InterruptedException e) {
	          // TODO Auto-generated catch block
	          e.printStackTrace();
	        }        
	   }
     }
        
	 public void shutDown(){
	      flag=true;
	    }
     }
	  //消费者
    public static class Consumer implements Runnable{
	    private final BlockingQueue<Integer> blockingQueue;
	    private volatile boolean flag;
	    public Consumer(BlockingQueue<Integer> blockingQueue) {
	      this.blockingQueue = blockingQueue;
	    }
    public void run() {
	      while(!flag){
47	        int info;
48	        try {
49	          info = blockingQueue.take();
50	          System.out.println(Thread.currentThread().getName()+" consumer "+info);
51	          Thread.sleep(50);
52	        } catch (InterruptedException e) {
53	          // TODO Auto-generated catch block
54	          e.printStackTrace();
55	        }        
56	      }
57	    }
58	    public void shutDown(){
59	      flag=true;
60	    }
61	  }
62	  public static void main(String[] args){
63	    BlockingQueue<Integer> blockingQueue = new LinkedBlockingQueue<Integer>(10);
64	    Producer producer=new Producer(blockingQueue);
65	    Consumer consumer=new Consumer(blockingQueue);
66	    //创建5个生产者，5个消费者
67	    for(int i=0;i<10;i++){
68	      if(i<5){
69	        new Thread(producer,"producer"+i).start();
70	      }else{
71	        new Thread(consumer,"consumer"+(i-5)).start();
72	      }
73	    }
74	 
75	    try {
76	      Thread.sleep(1000);
77	    } catch (InterruptedException e) {
78	      // TODO Auto-generated catch block
79	      e.printStackTrace();
80	    }
81	    producer.shutDown();
82	    consumer.shutDown();
83	 
84	  }
85	}
```

**阻塞队列原理：**
其实阻塞队列实现阻塞同步的方式很简单，使用的就是是lock锁的多条件（condition）阻塞控制。使用BlockingQueue封装了根据条件阻塞线程的过程，而我们就不用关心繁琐的await/signal操作了。
下面是Jdk 1.7中ArrayBlockingQueue部分代码：

```java
1	public ArrayBlockingQueue(int capacity, boolean fair) {
2	 
3	    if (capacity <= 0)
4	      throw new IllegalArgumentException();
5	    //创建数组  
6	    this.items = new Object[capacity];
7	    //创建锁和阻塞条件
8	    lock = new ReentrantLock(fair);  
9	    notEmpty = lock.newCondition();
10	    notFull = lock.newCondition();
11	  }
12	//添加元素的方法
13	public void put(E e) throws InterruptedException {
14	    checkNotNull(e);
15	    final ReentrantLock lock = this.lock;
16	    lock.lockInterruptibly();
17	    try {
18	      while (count == items.length)
19	        notFull.await();
20	      //如果队列不满就入队
21	      enqueue(e);
22	    } finally {
23	      lock.unlock();
24	    }
25	  }
26	 //入队的方法
27	 private void enqueue(E x) {
28	    final Object[] items = this.items;
29	    items[putIndex] = x;
30	    if (++putIndex == items.length)
31	      putIndex = 0;
32	    count++;
33	    notEmpty.signal();
34	  }
35	 //移除元素的方法
36	 public E take() throws InterruptedException {
37	    final ReentrantLock lock = this.lock;
38	    lock.lockInterruptibly();
39	    try {
40	      while (count == 0)
41	        notEmpty.await();
42	      return dequeue();
43	    } finally {
44	      lock.unlock();
45	    }
46	  }
47	 //出队的方法
48	 private E dequeue() {
49	    final Object[] items = this.items;
50	    @SuppressWarnings("unchecked")
51	    E x = (E) items[takeIndex];
52	    items[takeIndex] = null;
53	    if (++takeIndex == items.length)
54	      takeIndex = 0;
55	    count--;
56	    if (itrs != null)
57	      itrs.elementDequeued();
58	    notFull.signal();
59	    return x;
60  }
```

双端阻塞队列（BlockingDeque）
concurrent包下还提供双端阻塞队列（BlockingDeque），和BlockingQueue是类似的，只不过BlockingDeque提供从任意一端插入或者抽取元素的队列。



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
        new Thread(new ThreadB()).start();
    }
}

```



