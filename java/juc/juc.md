
- [1、 什么是JUC](#1-什么是JUC)
- [2、 进程与线程](#2-进程与线程)
  * [开启线程方式](#开启线程方式)
  * [Java真的可以开启线程吗？](#Java真的可以开启线程吗？)
  * [并发编程：并发、并行](#并发编程：并发、并行)
- [3、LOCK锁（重点）](#3-lock-----)
- [4、生产者和消费者问题](#4-生产者和消费者问题)
- [5、8锁现象](#5-8---)
- [6、集合类不安全](#6-集合类不安全)
- [7 Callable](#7-callable)
- [8、常用的辅助类](#8-常用的辅助类)
  * [8.1 CountDownLatch](#81-countdownlatch)
  * [8.2 CyclicBarrier](#82-cyclicbarrier)
  * [8.3 Semaphore](#83-semaphore)
- [9、读写锁](#9-读写锁)
- [10、阻塞队列](#10-阻塞队列)
- [11、线程池](#11-线程池)
- [13、流式计算](#13-流式计算)
- [14、 ForkJoin](#14--forkjoin)
- [15、异步回调](#15-异步回调)
- [16、JMM](#16-jmm)
- [17、volatile](#17-volatile)
- [18、彻底玩转单例模式](#18-彻底玩转单例模式)
- [19、深入理解CAS](#19-深入理解CAS)
- [20、原子引用](#20-原子引用)
- [21、各种锁的理解](#21-各种锁的理解)
  * [1、公平锁、非公平锁](#1-公平锁-非公平锁)
  * [2、可重入锁](#2-可重入锁)
  * [3、自旋锁](#3-自旋锁)
  * [4、死锁](#4-死锁)


## 1、 什么是JUC

java.util.concurrent（简称JUC ）包，在此包中增加了在并发编程中很常用的实用工具类

Runnable 没有返回值、效率相比 Callable 相对较低

## 2、 进程与线程  

一个进程往往可以包含多个线程，至少包含一个
java默认有几个线程？2个main、GC

### 开启线程方式  
Thread Runnable Callable
### Java真的可以开启线程吗？  
本地 native方法开启线程、底层的C++ Java无法直接操作硬件
### 并发编程：并发、并行

并发（多线程操作同一个资源）
    CPU一核，模拟出来多条线程，天下武功，唯快不破，快速交替

并行（多个人一起走）
    CPU多核，多个线程可以同时执行：线程池
   ```
    public static void main(String[] args){
            //获取CPU的核数
            //CUP密集型、IO密集型
            System.out.println(Runtime.getRuntime().availableProcessors());
    }
   ```

并发编程的本质:充分利用CPU的资源
   >线程有几个状态

   ```
public enum State {
             //新生
             NEW,
             //运行
             RUNNABLE,
             //阻塞
             BLOCKED,
             //等待
             WAITING,
             //超时等待
             TIMED_WAITING,
             //终止
             TERMINATED;
         }
   ```

   >wait/sleep  

   1、来自不同的类
   wait=>Object
   sleep=>Thread
   2、wait 会释放锁、sleep睡觉了，抱着锁睡觉，不会释放锁
   3、使用范围不同
   wait 必须在同步代码块中
   sleep 可以在任何地方睡


## 3、LOCK锁（重点）

> 传统Synchronized  

<img src=".\img\lock.jpg" width="80%">

<img src=".\img\lock2.jpg" width="80%">

公平锁：先来后到

非公平锁：可以插队（默认）



> Synchronized 和 Lock区别

1、Synchronized 内置的java关键字，Lock是java类

2、Synchronized无法判断获取锁的状态，Lock可以判断是否获取到了锁

3、Synchronized会自动释放锁，Lock必须要手动释放锁，如果不释放锁，**死锁**

4、Synchronized 线程1（获得锁，阻塞）、线程2（等待、傻傻的等）；Lock不会一直等下去

5、Synchronized 可重入锁，不可以中断的，非公平；Lock 可重入锁，不可以中断的，非公平(可以自己设置）

6、Synchronized 适合锁少量的代码同步问题；Lock适合锁大量的同步代码

> 锁是什么，如何判断锁的是谁

## 4、生产者和消费者问题

面试的：单例模式、排序算法、生产者消费者、死锁

Synchronized  版

```
class Data {
    private int number = 0;

    //+1
    public synchronized void increament() throws InterruptedException {
        if (number != 0) { //0
            this.wait();
        }
        number++;
        System.out.println(Thread.currentThread().getName() + "=>" + number);
        //通知其他线程，我+1完毕了
        this.notifyAll();
    }

    public synchronized void decreament() throws InterruptedException {
        if (number == 0) { //1
            this.wait();
        }
        number--;
        System.out.println(Thread.currentThread().getName() + "=>" + number);
        //通知其他线程，我-1完毕了
        this.notifyAll();
    }
}
```

> 存在问题，A B C D 4个线程

<img src=".\img\wait_xujia.jpg" width="80%">

if改成while

> JUC版  Lock

<img src=".\img\lock3.jpg" width="60%">

```
class Data {
    private int number = 0;
    Lock lock=new ReentrantLock();
    Condition condition=lock.newCondition();

    //+1
    public  void increament() throws InterruptedException {
        //选中ctrl+alt+t
        lock.lock();
        try {
            while (number != 0) { //0
                condition.await();
            }
            number++;
            System.out.println(Thread.currentThread().getName() + "=>" + number);
            //通知其他线程，我+1完毕了
            condition.signalAll();
        }catch (Exception e){
            e.printStackTrace();
        }finally {
            lock.unlock();
        }
    }

    public void decreament() throws InterruptedException {
        lock.lock();
        try {
            while (number == 0) { //1
                condition.await();
            }
            number--;
            System.out.println(Thread.currentThread().getName() + "=>" + number);
            //通知其他线程，我-1完毕了
            condition.signalAll();
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            lock.unlock();
        }

    }
```

**任何一个新的技术，绝对不仅仅只是覆盖原有的技术，优势和补充**

> Condition 精准的通知和唤醒线程

```
class Data3 {
    Lock lock = new ReentrantLock();
    Condition condition1 = lock.newCondition();
    Condition condition2 = lock.newCondition();
    Condition condition3 = lock.newCondition();
    private int number = 1;//1A 2B 3C

    public void printA() {
        try {
            lock.lock();
            //业务， 判断->执行->通知
            while (number != 1) {
                //等待
                condition1.await();
            }
            System.out.println(Thread.currentThread().getName() + "AAAAA");
            //唤醒，唤醒指定的人，B
            number = 2;
            condition2.signal();
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            lock.unlock();
        }
    }
    public void printB() {
        try {
            lock.lock();
            //业务， 判断->执行->通知
            while (number != 2) {
                //等待
                condition2.await();
            }
            System.out.println(Thread.currentThread().getName() + "BBBBB");
            //唤醒，唤醒指定的人，C
            number = 3;
            condition3.signal();
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            lock.unlock();
        }
    }
    public void printC() {
        try {
            lock.lock();
            //业务， 判断->执行->通知
            while (number != 3) {
                //等待
                condition3.await();
            }
            System.out.println(Thread.currentThread().getName() + "CCCCC");
            //唤醒，唤醒指定的人，A
            number = 1;
            condition1.signal();
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            lock.unlock();
        }
    }
}
```

## 5、8锁现象

```
// synchronized 锁的对象是方法的调用者,锁对象
public synchronized void sendSms(){
    System.out.println("sendSms");
}
```

```
//synchronized 锁的对象是Class
public static synchronized void call(){
    System.out.println("call");
}
```

```
//没有锁，不受锁的影响
public void hello(){
    System.out.println("hello");
}
```

## 6、集合类不安全

List

```
/**
 * 并发下ArrayList不安全 Synchronized
 * 1、List<String> list=new Vector<>();
 * 2、List<String> list= Collections.synchronizedList(new ArrayList<>())
 * 3、List<String> list=new CopyOnWriteArrayList<>();
 */
//写入时复制  COW
//多个线程调用的时候，list,读取的时候，固定的，写入（覆盖）
//再写入的时候避免覆盖，造成数据问题
//读写分离
//CopyOnWriteArrayList 比 Vector(Synchronized) 
```

Set

```
/**
 * Set<String> set=new HashSet<>();
 * 1、Set<String> set= Collections.synchronizedList(new HashSet<>())
 * 2、Set<String> set=new CopyOnWriteArraySet<>();
 */
```

hashSet 底层是什么？

```
public HashSet() {
    map = new HashMap<>();
}
```

//add set 本质就是map key是无法重复的

```
public boolean add(E e) {
    return map.put(e, PRESENT)==null;
}
```

```
private static final Object PRESENT = new Object();
```

Map

```
/**
 *Map<String,String> map=new HashMap<String, String>(16,0.75F);
 *1、Collections.synchronizedMap(new HashMap<>());
 *2、Map<String,String> map=new ConcurrentHashMap<>();
 */
```



## 7 Callable 

<img src=".\img\callable.png" width="80%">


1、可以返回值

2、可以抛出异常

3、方法不同，run()/ call()

```
public class CallableTest {
    public static void main(String[] args) {
        MyCallable callable=new MyCallable();
        FutureTask task=new FutureTask(callable);//适配器
        new Thread(task,"A").start();
        new Thread(task,"B").start();//结果会被缓存，效率高

        try {
            String o= (String) task.get();//阻塞方法
        } catch (InterruptedException e) {
            e.printStackTrace();
        } catch (ExecutionException e) {
            e.printStackTrace();
        }
    }
}

class MyCallable implements Callable<String>{
    @Override
    public String call() throws Exception {
    System.out.println("call");//只会打印一次
    //耗时的操作
        return "1234";
    }
}
```

**细节**

1、有缓存

2、结果可能需要等待，会阻塞



## 8、常用的辅助类

### 8.1 CountDownLatch  

<img src=".\img\countdownlatch.jpg" width="90%">

```
public static void main(String[] args) throws InterruptedException {
    CountDownLatch countDownLatch = new CountDownLatch(6);
    for (int i = 0; i < 6; i++) {
        new Thread(() -> {
            System.out.println(Thread.currentThread().getName() + "Go Out");
            countDownLatch.countDown();//数量-1
        },String.valueOf(i)).start();
    }
    countDownLatch.await();//等待计数器归零，然后再向下执行

    System.out.println("close Door");
}
```

### 8.2 CyclicBarrier  

<img src=".\img\cyclicbarrier.jpg" width="90%">

```
public static void main(String[] args) {
    CyclicBarrier cyclicBarrier = new CyclicBarrier(7, new Runnable() {
        @Override
        public void run() {
            System.out.println("召唤神龙成功！");
        }
    });
    for (int i = 0; i < 7; i++) {
        final int finalI = i;//作用域问题
        new Thread(() -> {
            System.out.println(Thread.currentThread().getName() + "收集" + finalI + "个龙珠");
            try {
                cyclicBarrier.await();
            } catch (InterruptedException e) {
                e.printStackTrace();
            } catch (BrokenBarrierException e) {
                e.printStackTrace();
            }
        }).start();
    }
}
```



### 8.3 Semaphore 

```
public static void main(String[] args) {
    //线程数量：停车位 限流
    Semaphore semaphore = new Semaphore(3);
    for (int i = 1; i <= 6; i++) {
        new Thread(() -> {
            try {
                semaphore.acquire();//得到
                System.out.println(Thread.currentThread().getName() + "抢到车位");
                TimeUnit.SECONDS.sleep(2);
                System.out.println(Thread.currentThread().getName() + "离开车位");
            } catch (InterruptedException e) {
                e.printStackTrace();
            } finally {
                semaphore.release();//释放
            }

        }, String.valueOf(i)).start();
    }
}
```

原理：

`semaphore.acquire()` 获得，假设已经满了，等待，等待被释放为止

`semaphore.release()`释放，会将当前的信号量释放+1，然后唤醒等待的线程

作用：

1、多个共享资源互斥

2、并发限流，控制最大的线程数

## 9、读写锁

`ReadWriteLock`

<img src=".\img\readwrite.jpg" width="90%">

```

/**
 * 独占锁（写锁）一次只能被一个线程占有
 * 共享锁（读锁）多个线程可以同时占有
 */
public class ReadWriteLockDemo {
    public static void main(String[] args) {
        MyCache myCache=new MyCache();
        for (int i = 0; i < 5; i++) {
            int finalI = i;
            new Thread(()->{
                myCache.put(finalI +"", finalI +"");
            },String.valueOf(i)).start();
        }

        for (int i = 0; i < 5; i++) {
            int finalI = i;
            new Thread(()->{
                myCache.get(finalI +"");
            },String.valueOf(i)).start();

        }


    }
}

class MyCache{
    private volatile Map<String,Object> map=new HashMap<>();
    //读写锁：更加细粒度的控制
    private ReadWriteLock lock=new ReentrantReadWriteLock();
    //存，写入的时候，只希望同时只有一个线程写
    public void put(String key,Object value){
        lock.writeLock().lock();
        try {
            System.out.println(Thread.currentThread().getName()+"写入"+key);
            map.put(key,value);
            System.out.println(Thread.currentThread().getName()+"写入OK"+key);
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            lock.writeLock().unlock();
        }

    }

    public void get(String key){
        lock.readLock().lock();
        try {
            System.out.println(Thread.currentThread().getName()+"读取"+key);
            Object o=map.get(key);
            System.out.println(Thread.currentThread().getName()+"读取OK"+key);
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            lock.readLock().unlock();
        }
    }

}
```

## 10、阻塞队列

<img src="./img/blockingqueue.jpg" style="zoom:50%;" />

阻塞

队列

> BlockingQueue

<img src=".\img\bloking.jpg" width="80%">

<img src=".\img\queue.png" width="80%">

什么情况下我们会使用阻塞队列：多线程并发处理，线程池

**学会使用队列**

添加移除

**四组API**

| 方式       | 抛出异常 | 有返回值，不抛出异常 | 阻塞等待 | 超时等待             |
| ---------- | -------- | -------------------- | -------- | -------------------- |
| 添加       | add      | offer()              | put      | offer( , , )重载方法 |
| 移除       | remove   | poll()               | take     | poll( , )重载方法    |
| 判断队列首 | element  | peek                 |          |                      |

```
/**
     *抛出异常
     */
    public static void test1(){
        ArrayBlockingQueue blockingQueue=new ArrayBlockingQueue<>(2);
        System.out.println(blockingQueue.add("a"));
        System.out.println(blockingQueue.add("b"));

//        IllegalStateException Queue full 抛出异常
//        System.out.println(blockingQueue.add("d"));
        System.out.println(blockingQueue.element());  //查看队首元素  为空抛出异常

        System.out.println(blockingQueue.remove());
        System.out.println(blockingQueue.remove());

//        NoSuchElementException 抛出异常
//        System.out.println(blockingQueue.remove());
    }
```

```
/**
     * 不抛出异常
     */
    public static void test2(){
        ArrayBlockingQueue blockingQueue=new ArrayBlockingQueue<>(2);
        System.out.println(blockingQueue.offer("a"));
        System.out.println(blockingQueue.offer("b"));
        System.out.println(blockingQueue.offer("c")); //false 不抛出异常

        System.out.println(blockingQueue.peek());   //查看队首元素

        System.out.println(blockingQueue.poll());
        System.out.println(blockingQueue.poll());
        System.out.println(blockingQueue.poll());  //null 不抛出异常
    }
```

```
 /**
     * 等待，阻塞（一直阻塞）
     */
    public static void test3() throws InterruptedException {
        ArrayBlockingQueue blockingQueue=new ArrayBlockingQueue<>(2);
        blockingQueue.put("a");
        blockingQueue.put("b");
//        blockingQueue.put("c");    //队列没有位置了，一直阻塞

//        System.out.println(blockingQueue.take());   //查看队首元素

        System.out.println(blockingQueue.take());
        System.out.println(blockingQueue.take());
//        System.out.println(blockingQueue.take());  //队列没有数据了，一直阻塞
    }
```

```
/**
     * 等待，阻塞（等待超时）
     */
    public static void test4() throws InterruptedException{
        ArrayBlockingQueue blockingQueue=new ArrayBlockingQueue<>(2);
        System.out.println(blockingQueue.offer("a"));
        System.out.println(blockingQueue.offer("b"));
        System.out.println(blockingQueue.offer("c",2, TimeUnit.SECONDS)); //等待超时两秒就退出

        System.out.println("============");
//
        System.out.println(blockingQueue.poll());
        System.out.println(blockingQueue.poll());
        System.out.println(blockingQueue.poll(2,TimeUnit.SECONDS));  //等待超时两秒就退出
    }
```

> SynchronousQueue同步队列

```
/**
 * 同步队列
 * 和其他的BlockQueue 不一样，SynchronousQueue不存储元素
 * put了一个元素，必须从里面先take取出来，否则不能再put进去值！
 */
public class Synchronous {
    public static void main(String[] args) {
        BlockingQueue<String> strings = new SynchronousQueue<>();//同步队列
        new Thread(() -> {
            try {
                System.out.println(Thread.currentThread().getName() + " put 1");
                strings.put("1");
                System.out.println(Thread.currentThread().getName() + " put 2");
                strings.put("2");
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }, "T1").start();

        new Thread(() -> {
            try {
                TimeUnit.SECONDS.sleep(3);
                System.out.println(Thread.currentThread().getName() + "=" + strings.take());
                TimeUnit.SECONDS.sleep(3);
                System.out.println(Thread.currentThread().getName() + "=" + strings.take());
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }, "T2").start();
    }
}
```

## 11、线程池

> 池化技术

程序的运行，本质：占用系统的资源

优化资源的使用：池化技术=>线程池、连接池、内存池、对象池（创建、销毁 十分良妃资源）

池化技术：事先准备好一些资源，有人要用，就来我这拿，用完之后还给我

**线程池的好处**

1、降低资源的消耗

2、提高相应的速度

3、方便管理

**线程复用、可以控制并发数、管理线程**

> 线程池：三大方法

```
/**
 * Executors工具类、3大方法
 */
public class PoolDemo {
    public static void main(String[] args) {
        ExecutorService threadPool = Executors.newSingleThreadExecutor();//单个线程
//        Executors.newFixedThreadPool(5);//创建一个固定的线程池的大小
//        Executors.newCachedThreadPool();//可伸缩的
//        Executors.newScheduledThreadPool(2);

        try {
            for (int i = 0; i < 10; i++) {
                threadPool.execute(()->{
                    System.out.println(Thread.currentThread().getName()+"ok");
                });
            }
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            //线程池用完，程序结束，关闭线程池
            threadPool.shutdown();
        }
        
    }
}
```

> 7大参数

源码分析

```
public static ExecutorService newSingleThreadExecutor() {
    return new FinalizableDelegatedExecutorService
        (new ThreadPoolExecutor(1, 1,
                                0L, TimeUnit.MILLISECONDS,
                                new LinkedBlockingQueue<Runnable>()));
}
```

```
public static ExecutorService newFixedThreadPool(int nThreads) {
    return new ThreadPoolExecutor(nThreads, nThreads,
                                  0L, TimeUnit.MILLISECONDS,
                                  new LinkedBlockingQueue<Runnable>());
}
```

```
public static ExecutorService newCachedThreadPool() {
    return new ThreadPoolExecutor(0, Integer.MAX_VALUE,
                                  60L, TimeUnit.SECONDS,
                                  new SynchronousQueue<Runnable>());
}
```

```
public ScheduledThreadPoolExecutor(int corePoolSize) {
    super(corePoolSize, Integer.MAX_VALUE, 0, NANOSECONDS,
          new DelayedWorkQueue());
}
```

// 本质 ThreadPoolExecutor  七大参数

```
public ThreadPoolExecutor(int corePoolSize,//核心线程池大小
                          int maximumPoolSize,//最大核心线程池大小
                          long keepAliveTime,//超时了，没有人调用就会释放
                          TimeUnit unit,//超市单位
                          BlockingQueue<Runnable> workQueue,//阻塞队列
                          ThreadFactory threadFactory,//线程工厂，创建线程的，一般不用动
                          RejectedExecutionHandler handler) {//拒绝策略
    if (corePoolSize < 0 ||
        maximumPoolSize <= 0 ||
        maximumPoolSize < corePoolSize ||
        keepAliveTime < 0)
        throw new IllegalArgumentException();
    if (workQueue == null || threadFactory == null || handler == null)
        throw new NullPointerException();
    this.corePoolSize = corePoolSize;
    this.maximumPoolSize = maximumPoolSize;
    this.workQueue = workQueue;
    this.keepAliveTime = unit.toNanos(keepAliveTime);
    this.threadFactory = threadFactory;
    this.handler = handler;
}
```

<img src=".\img\threadpool.jpg" style="zoom:50%"/>

> 手动创建一个线程池

```
public class PoolDemo {
    public static void main(String[] args) {
        //自定义线程池
        ThreadPoolExecutor threadPool = new ThreadPoolExecutor(
                2,
                5,
                3,
                TimeUnit.SECONDS,
                new LinkedBlockingDeque<>(3),
                Executors.defaultThreadFactory(),
                new ThreadPoolExecutor.CallerRunsPolicy()
                );
        try {
            //最大承载：Deque + max
            for (int i = 0; i < 100; i++) {
                threadPool.execute(() -> {
                    System.out.println(Thread.currentThread().getName() + "ok");
                });
            }
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            //线程池用完，程序结束，关闭线程池
            threadPool.shutdown();
        }
    }
}
```

> 4中拒绝策略

```
/**
 * 1、new ThreadPoolExecutor.AbortPolicy()
 * //银行满了，还有人进来，不处理这个人的，抛出异常
 * //超过RejectedExecutingException
 *
 *  2、new ThreadPoolExecutor.CallerRunsPolicy()//哪来的去哪里
 *
 *  3、new ThreadPoolExecutor.DiscardPolicy //队列满了，丢掉任务，不会抛出异常！
 *
 *  4、new ThreadPoolExecutor.DiscardOldestPolicy //队列满了，尝试去和最早的竞争，也不会抛出异常
 */
```

> 小结和拓展

```
//最大线程到底如何定义
//1、CPU 密集型，机核，就是几，可以保证CPU的效率最高！
//maximumPoolSize=Runtime.getRuntime().availableProcessors()
//（1、任务管理器->CPU->逻辑处理器数量 2、电脑->管理->设备管理器->处理器）
//2、IO 密集行（判断你程序中十分耗IO的线程，一般是两倍）
//
```

12、四大函数式接口（必需掌握）

新时代的程序员：lambda、链式编程、函数式接口、Stream流式计算

> 函数式接口：只有一个方法的接口

```
@FunctionalInterface
public interface Runnable {
    public abstract void run();
}
//简化编程模型，在新版本的框架底层大量应用
//forEach(Consumer<? super T> action)
```

<img src=".\img\function.jpg" style="zoom:70%"/>

**代码测试**

> Function函数式接口

```
//Function 函数式接口，工具类：输出输入的值
//只要是 函数式接口 可以 用lambda表达式
public class FunctionDemo {
    public static void main(String[] args) {
        Function<String,String> function = (o) -> o;
        System.out.println(function.apply("abc"));
    }
}
```

>Predicate

```
/**
 * 断定型接口：有一个输入参数，返回值只能是 布尔值
 */
public class FunctionDemo02 {
    //判断字符串是否为空
    public static void main(String[] args) {
        Predicate<String> function = (str) -> str.isEmpty();
        System.out.println(function.test(""));
    }
}
```

> Consummer 消费型接口

```
/**
 * Consumer 只有输入 没有返回值
 */
public class FunctionDemo03 {
    public static void main(String[] args) {
        Consumer<String> function = (str) -> {System.out.println(str);};
        function.accept("abc");
    }
}
```

>Supplier 供给型接口

```
/**
 * Supplier 没有输入 只有返回值
 */
public class FunctionDemo04 {
    public static void main(String[] args) {
        Supplier<String> function = () -> "1024";
        function.get();
    }
}
```

## 13、流式计算

> 什么是Stream流式计算

大数据：存储 + 计算

集合、MySQL本质就是存储东西的

计算都应该交给流来操作

```
/**
 * 一分钟内完成，只能用一行代码完成
 * 1、ID必须是偶数
 * 2、年龄必须大于23岁
 * 3、用户名转为大写字母
 * 4、用户名字母倒着排序
 * 5、只能输出一个用户！
 */
public class Test {
    public static void main(String[] args) {
        User u1 = new User(1, "a", 21);
        User u2 = new User(2, "b", 22);
        User u3 = new User(3, "c", 23);
        User u4 = new User(4, "d", 24);
        User u5 = new User(5, "e", 25);
        User u6 = new User(6, "f", 26);

        Arrays.asList(u1, u2, u3, u4, u5, u6).stream()
                .filter(user -> user.getId() % 2 == 0 && user.getAge() > 23)
                .map(user -> user.getName().toUpperCase())
                .sorted((o1, o2) -> o2.compareTo(o1))
                .limit(1)
                .forEach(System.out::println);
    }
}
```

## 14、 ForkJoin

> 什么是 ForkJoin

ForkJoin在JDK 1.7 ，并行执行任务，提高效率，大数据量

大数据：Map Reduce(把大人物拆分成小任务)

<img src=".\img\forkjoin.jpg"  width="50%"/>

> ForkJoin 特点：动作窃取

<img src=".\img\forkjoin01.jpg" width="20%"/>

<img src=".\img\forkjointask.jpg" width="50%">

```
/**
 * 求和计算的任务
 * 3000  6000 ForkJoin  9000 Stream并行流
 * 1、ForkJoinPool 通过他来执行
 * 2、pool.execute(ForkJoinTask<?> task)
 * 3、计算类要继承 ForkJoinTask
 */
public class ForkJoinDemo extends RecursiveTask<Long> {
    private Long start;
    private Long end;
    //临界值
    private Long temp = 10000L;

    public ForkJoinDemo(Long start, Long end) {
        this.start = start;
        this.end = end;
    }
    //计算方法
    @Override
    protected Long compute() {
        if ((end - start) < temp) {
            Long sum = 0L;
            for (Long i = start; i <= end; i++) {
                sum += i;
            }
            return sum;
        } else {//forkjoin 递归
            long middle = (start + end) / 2;
            ForkJoinDemo task1 = new ForkJoinDemo(start, middle);
            task1.fork();//拆分任务压入线程队列
            ForkJoinDemo task2 = new ForkJoinDemo(middle+1, end);
            task2.fork();
            return task1.join()+task2.join();
        }
    }
}
```





```
public class Test {
    public static void main(String[] args) throws ExecutionException, InterruptedException {
//        test1();//2044
//        test2();//738
        test3();//223
    }

    //普通程序员
    public static void test1() {
        long start = System.currentTimeMillis();
        Long sum = 0L;
        for (Long i = 1L; i <= 1_0000_0000; i++) {
            sum += i;
        }
        long end = System.currentTimeMillis();
        System.out.println("sum=" + sum + "时间：" + (end - start));
    }

    //会使用forkjoin
    public static void test2() throws ExecutionException, InterruptedException {
        long start = System.currentTimeMillis();
        ForkJoinPool forkJoinPool = new ForkJoinPool();
        ForkJoinTask forkJoinTask = new ForkJoinDemo(0L, 1_0000_0000L);
//        forkJoinPool.execute(forkJoinTask);  //同步
        ForkJoinTask<Long> submit = forkJoinPool.submit(forkJoinTask);  //异步
        Long sum = submit.get();
        long end = System.currentTimeMillis();
        System.out.println("sum=" + sum + "时间：" + (end - start));
    }

    //会使用Stream流式计算
    public static void test3() {
        long start = System.currentTimeMillis();
        //Stream 并行流 ()  (]
        long sum = LongStream.rangeClosed(0L, 1_0000_0000).parallel().reduce(0, Long::sum);
        long end = System.currentTimeMillis();
        System.out.println("sum=" + sum + "时间：" + (end - start));
    }
}
```

## 15、异步回调

```
CompletableFuture
```

## 16、JMM

> 请你谈谈你对Volatile的理解

Volatile 是 Java 虚拟机提供的**轻量级的同步机制**

1、保证可见性

<font color="red">2、不保证原子性</font>

3、禁止指令重排

>什么是JMM

JMM 是 Java内存模型，不存在的东西，概念！约定！

**关于JMM的一些同步的约定**

1、线程解锁前，必须把共享变量**立刻**刷回主存

2、线程枷锁前，必须读取主存中的最新值到工作内存中

3、枷锁和解锁是同一把锁

线程 **工作内存**、**主存**

**8种操作**

<img src=".\img\jmm.jpg" width="70%">



 **内存交互操作有8种，虚拟机实现必须保证每一个操作都是原子的，不可在分的（对于double和long类型的变量来说，load、store、read和write操作在某些平台上允许例外）**

- lock   （锁定）：作用于主内存的变量，把一个变量标识为线程独占状态

- unlock （解锁）：作用于主内存的变量，它把一个处于锁定状态的变量释放出来，释放后的变量才可以被其他线程锁定
- read  （读取）：作用于主内存变量，它把一个变量的值从主内存传输到线程的工作内存中，以便随后的load动作使用
- load   （载入）：作用于工作内存的变量，它把read操作从主存中变量放入工作内存中
- use   （使用）：作用于工作内存中的变量，它把工作内存中的变量传输给执行引擎，每当虚拟机遇到一个需要使用到变量的值，就会使用到这个指令
- assign （赋值）：作用于工作内存中的变量，它把一个从执行引擎中接受到的值放入工作内存的变量副本中
- store  （存储）：作用于主内存中的变量，它把一个从工作内存中一个变量的值传送到主内存中，以便后续的write使用
- write 　（写入）：作用于主内存中的变量，它把store操作从工作内存中得到的变量的值放入主内存的变量中

**JMM对这八种指令的使用，制定了如下规则：**

- 不允许read和load、store和write操作之一单独出现。即使用了read必须load，使用了store必须write

- 不允许线程丢弃他最近的assign操作，即工作变量的数据改变了之后，必须告知主存
- 不允许一个线程将没有assign的数据从工作内存同步回主内存
- 一个新的变量必须在主内存中诞生，不允许工作内存直接使用一个未被初始化的变量。就是怼变量实施use、store操作之前，必须经过assign和load操作
- 一个变量同一时间只有一个线程能对其进行lock。多次lock后，必须执行相同次数的unlock才能解锁
- 如果对一个变量进行lock操作，会清空所有工作内存中此变量的值，在执行引擎使用这个变量前，必须重新load或assign操作初始化变量的值
- 如果一个变量没有被lock，就不能对其进行unlock操作。也不能unlock一个被其他线程锁住的变量
- 对一个变量进行unlock操作之前，必须把此变量同步回主内存

## 17、volatile

> 1、保证可见性

> 2、不保证原子性

原子性：不可分割

**如果不加lock和synchronized,怎么保证原子性**

使用原子类，解决原子性问题

```
public class VDemo {
    private volatile static AtomicInteger num=new AtomicInteger();
    public static void add(){
        //num++;不是原子性操作
        num.getAndIncrement();   CAS
    }

    public static void main(String[] args) {
        for (int i = 0; i < 20; i++) {
            new Thread(()->{
                for (int j = 0; j < 1000; j++) {
                    add();
                }
            }).start();
        }
        while (Thread.activeCount()>2){//main线程  gc线程
            Thread.yield();//礼让
        }
        System.out.println(Thread.currentThread().getName()+" "+num);
    }
}
```

查看字节码

cd D:\springBoot\JUCDemo\target\classes\com\example\juc\volat\VDemo.class

javap -c VDemo.class 

这些类的底层都直接和操作系统挂钩！在内存中修改值！Unsafe类是一个很特殊的存在

> 指令重排

什么是指令重排：**你写的程序，计算机并不是按照你写的那样去去执行的。**

源代码->编译器优化的重排->指令并行也可能会重排->内存系统也会重排->执行

<font color="red">处理器在进行指令重排的时候考虑：数据之间的依赖性！</font>

volatile可以避免指令重排：

内存屏障、CPU指令、作用：

1、保证特定的操作的执行顺序

2、可以保证某些变量的内存可见性（利用这些特性volatile实现了可见性）

<img src=".\img\volatile_yuanli.png" width="30%">

**Volatile是可以保证 可见性、不能保证原子性，由于内存屏障，可以保证避免指令重排现象产生**

<font color="red">volatile在单例模式用的最多</font>

## 18、彻底玩转单例模式

饿汉式、DCL懒汉式

> 饿汉式

```
//饿汉式单例
public class Hungry {
    private byte[] data1=new byte[1024*1024];
    private Hungry(){}

    private final static Hungry HUNGRU=new Hungry();

    public static Hungry getINstance() {
        return HUNGRU;
    }
}
```

> DCL懒汉

```
//饿汉式单例
public class LazyMan {
    private LazyMan() {
        //防止反射
        synchronized (LazyMan.class) {
            if (lazyMan != null) {
                throw new RuntimeException("不要使用反射破坏单例");
            }
        }

        System.out.println(Thread.currentThread().getName() + "OK");
    }

    private volatile static LazyMan lazyMan;

    //双重检测锁模式的  懒汉式单例 DCL懒汉式
    public static LazyMan getInstance() {
        if (lazyMan == null) {
            synchronized (LazyMan.class) {
                if (lazyMan == null) {
                    lazyMan = new LazyMan();//不是原子性操作
                }
            }
        }

        return lazyMan;
    }

    /**
     * 1、分配内存空间
     * 2、执行构造方法，初始化对象
     * 3、把这个对象指向这个空间
     * <p>
     * 123
     * 132  A
     * B //此时lazyMan还没有完成构造
     */


    public static void main(String[] args) throws NoSuchMethodException, IllegalAccessException, InvocationTargetException, InstantiationException {
        //反射 不安全
        Constructor<LazyMan> lazyManConstructor = LazyMan.class.getDeclaredConstructor(null);
        lazyManConstructor.setAccessible(true);//无视私有构造器
        LazyMan lazyMan1 = lazyManConstructor.newInstance();
        lazyManConstructor.setAccessible(true);//无视私有构造器
        LazyMan lazyMan2 = lazyManConstructor.newInstance();
        System.out.println(lazyMan1);
        System.out.println(lazyMan2);
    }
}
```

> 静态内部类

```
//静态内部类
public class Holder {
    private Holder(){

    }

    public static Holder getInstance(){
        return InnerClass.HOLDER;
    }
    public static class InnerClass{
        private static final Holder HOLDER=new Holder();
    }
}
```

> 枚举

```
/**
 * 枚举不能被反射
 */
public enum  EnumSingle {
    INSTANCE;
    public EnumSingle getInstance(){
        return INSTANCE;
    }
}

class Test{
    public static void main(String[] args) throws NoSuchMethodException, IllegalAccessException, InvocationTargetException, InstantiationException {
        EnumSingle instance1=EnumSingle.INSTANCE;
        System.out.println(instance1);
//        jad.exe  反编译class ->java
        Constructor<EnumSingle> lazyManConstructor = EnumSingle.class.getDeclaredConstructor(String.class,int.class);
        lazyManConstructor.setAccessible(true);//无视私有构造器
        EnumSingle lazyMan1 = lazyManConstructor.newInstance();
//        Cannot reflectively create enum objects   newInstance源码中查看
    }
}
```

## 19、深入理解CAS

> 什么是CAS

```
public static void main(String[] args) {
    AtomicInteger atomicInteger=new AtomicInteger(2020);
    //期望、更新
    //compareAndSet(int expect, int update)
    //如果是期望值，那么就更新，否则，就不更新
    //CAS 是CPU的并发原语
    System.out.println(atomicInteger.compareAndSet(2020,2021));
    System.out.println(atomicInteger.get());
    atomicInteger.getAndIncrement();

    System.out.println(atomicInteger.compareAndSet(2020,2021));
    System.out.println(atomicInteger.get());
}
```

> Unsafe类

<img src=".\img\atomic.jpg" width="60%">

<img src=".\img\unsafe.jpg" width="50%">

<img src=".\img\zixuansuo.jpg" width="50%">

CAS ：比较当前工作内存中的值和主存中的值，如果这个值是期望的，那么则执行操作！如果不是则一直循环

缺点：

1、循环会耗时

2、一次性只会保证一个共享变量的原子性

3、ABA问题



> CAS: ABA 问题（狸猫换太子）

<img src=".\img\ABA.jpg" width="50%">

##  20、原子引用

> 解决 ABA 问题，引入原子引用！对应思想乐观锁

带版本号的原子操作

```
//如果泛型是一个包装类，之一对象的引用问题
//        Integer 使用了对象缓存机制，默认范围是-128~127，推荐使用静态工厂方法valueOf获取对象实例，而不是new,因为valueOf使用缓存，而new一定会创建新的对象分配新的内存空间
        AtomicStampedReference<Integer> atomicInteger = new AtomicStampedReference<>(1, 1);

        new Thread(() -> {
            int stamp = atomicInteger.getStamp();//获得版本号
            System.out.println("a1=>" + stamp);
            try {
                TimeUnit.SECONDS.sleep(1);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            System.out.println(atomicInteger.compareAndSet(1, 2, atomicInteger.getStamp(), atomicInteger.getStamp() + 1));
            System.out.println("a2=>" + atomicInteger.getStamp());

            System.out.println(atomicInteger.compareAndSet(2, 1, atomicInteger.getStamp(), atomicInteger.getStamp() + 1));
            System.out.println("a3=>" + atomicInteger.getStamp());
        }, "A").start();
        //乐观锁的原理相同
        new Thread(() -> {
            int stamp = atomicInteger.getStamp();//获得版本号
            System.out.println("b1=>" + stamp);
            try {
                TimeUnit.SECONDS.sleep(2);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }

            System.out.println(atomicInteger.compareAndSet(1, 6, stamp, stamp + 1));
            System.out.println("b2=>" + atomicInteger.getStamp());

        }, "B").start();
```

## 21、各种锁的理解

### 1、公平锁、非公平锁

公平锁：不能插队，必须先来后到

非公平锁：可以插队（默认是非公平锁）

```
public ReentrantLock() {
    sync = new NonfairSync();
}
public ReentrantLock(boolean fair) {
        sync = fair ? new FairSync() : new NonfairSync();
    }
```

### 2、可重入锁

可重入锁（递归锁）

<img src=".\img\chongrusuo.jpg" width="50%">

> synchronized版

```
public class Demo01 {
    public static void main(String[] args) {
        Phone phone=new Phone();
        new Thread(() -> {
            phone.sms();
        },"A").start();
        new Thread(() -> {
            phone.sms();
        },"B").start();
    }
}

class Phone {
    public synchronized void sms() {
        System.out.println(Thread.currentThread().getName() + "sms");
        call();//这里也有锁
    }

    private synchronized void call() {
        System.out.println(Thread.currentThread().getName() + "call");
    }
}
```

> Lock版

```
class Phone2 {
    Lock lock = new ReentrantLock();

    public void sms() {
        lock.lock(); //细节问题：
        //lock 锁必须配对 否则就会死在里面
        try {
            System.out.println(Thread.currentThread().getName() + "sms");
            call();//这里也有锁
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            lock.unlock();
        }
    }

    private void call() {
        lock.lock();
        try {
            System.out.println(Thread.currentThread().getName() + "call");
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            lock.unlock();
        }
    }
}
```

### 3、自旋锁

spinlock

```
public final int getAndAddInt(Object var1, long var2, int var4) {
    int var5;
    do {
        var5 = this.getIntVolatile(var1, var2);
    } while(!this.compareAndSwapInt(var1, var2, var5, var5 + var4));

    return var5;
}
```

我们来自定义一个锁测试

```
/**
 * 自旋锁
 */
public class SpinlockDemo {
    //int 0
    //Thread null
    AtomicReference<Thread> atomicReference=new AtomicReference<>();

    //加锁
    public void myLock(){
        Thread thread =Thread.currentThread();
        System.out.println(Thread.currentThread().getName()+"=> mylock");
        while (!atomicReference.compareAndSet(null,thread)){

        }
    }
    //解锁
    public void myUnLock(){
        Thread thread =Thread.currentThread();
        System.out.println(Thread.currentThread().getName()+"=> myUnlock");
        atomicReference.compareAndSet(thread,null);
    }
}
```

> 测试

```
public class TestSpinLock {
    public static void main(String[] args) throws InterruptedException {
        //底层使用的自旋锁
        SpinlockDemo lock = new SpinlockDemo();
        new Thread(() -> {
            lock.myLock();
            try {
                TimeUnit.SECONDS.sleep(3);
            } catch (InterruptedException e) {
                e.printStackTrace();
            } finally {
                lock.myUnLock();
            }
            
        }, "T1").start();

        TimeUnit.SECONDS.sleep(1);

        new Thread(() -> {
            lock.myLock();
            try {
                TimeUnit.SECONDS.sleep(3);
            } catch (InterruptedException e) {
                e.printStackTrace();
            } finally {
                lock.myUnLock();
            }
        }, "T2").start();
    }
}
```

### 4、死锁

> 死锁是什么

<img src=".\img\sisuo.jpg" width="50%">

死锁测试，怎么排除死锁：

```
public class DeadLockDemo {
    public static void main(String[] args) {
        String locA = "lockA";
        String locB = "lockB";
        new Thread(new MyThread(locA, locB),"T1").start();
        new Thread(new MyThread(locB, locA),"T2").start();
    }
}

class MyThread implements Runnable {
    private String lockA;
    private String lockB;

    public MyThread(String lockA, String lockB) {
        this.lockA = lockA;
        this.lockB = lockB;
    }

    @Override
    public void run() {
        synchronized (lockA) {
            System.out.println(Thread.currentThread().getName() + "lock:" + lockA + " =>get " + lockB);
            try {
                TimeUnit.SECONDS.sleep(2);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            synchronized (lockB) {
                System.out.println(Thread.currentThread().getName() + "lock:" + lockB + " =>get " + lockA);
            }
        }
    }
}
```

> 解决问题

1、使用JDK bin    **jps -l**定位进程号

```D:\springBoot\test> jps -l
17504 org.jetbrains.jps.cmdline.Launcher
15860
19896 sun.tools.jps.Jps
20168 com.example.juc.lock.DeadLockDemo
9768 com.intellij.database.remote.RemoteJdbcServer
```

2、使用 **jstack** 进程号找到死锁问题,**一般在结果最后面**

```
"T2":
        at com.example.juc.lock.MyThread.run(DeadLockDemo.java:34)
        - waiting to lock <0x00000000d673a708> (a java.lang.String)
        - locked <0x00000000d673a740> (a java.lang.String)
        at java.lang.Thread.run(Thread.java:745)
"T1":
        at com.example.juc.lock.MyThread.run(DeadLockDemo.java:34)
        - waiting to lock <0x00000000d673a740> (a java.lang.String)
        - locked <0x00000000d673a708> (a java.lang.String)
        at java.lang.Thread.run(Thread.java:745)

Found 1 deadlock.
```

面试，工作中！排查问题：

1、日志

2、堆栈信息
