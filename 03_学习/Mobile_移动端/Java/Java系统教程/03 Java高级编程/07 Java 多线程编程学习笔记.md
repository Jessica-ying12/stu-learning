## 一、多线程概述

### 1.1 什么是多线程？

**多线程**：一个程序包含两个或多个能并发运行的部分，每个部分称为一个线程，每个线程定义独立的执行路径。

### 1.2 进程 vs 线程

|概念|定义|特点|
|---|---|---|
|**进程**|操作系统分配的内存空间，包含一个或多个线程|资源分配的基本单位|
|**线程**|进程中的执行单元，必须是进程的一部分|CPU 调度的基本单位|

### 1.3 多线程的优势

- ✅ 充分利用 CPU 资源
- ✅ 提高程序响应速度
- ✅ 改善用户体验
- ✅ 简化程序结构
- ✅ 最小化 CPU 空闲时间

---

## 二、线程的生命周期

### 2.1 线程状态图

```
新建(New) → 就绪(Runnable) → 运行(Running) → 死亡(Dead)
                ↑                    ↓
                └──── 阻塞(Blocked) ──┘
```

### 2.2 五种状态详解

|状态|说明|触发条件|
|---|---|---|
|**新建(New)**|线程对象被创建|`new Thread()`|
|**就绪(Runnable)**|等待 CPU 分配时间片|`thread.start()`|
|**运行(Running)**|获得 CPU，执行 run()|系统调度|
|**阻塞(Blocked)**|暂停执行，让出 CPU|sleep/wait/suspend|
|**死亡(Dead)**|线程执行完毕或被终止|run() 结束/stop()|

### 2.3 阻塞状态的三种情况

1. **睡眠**：`sleep(long millis)` - 指定时间后自动唤醒
2. **等待**：`wait()` - 需要 `notify()` 或 `notifyAll()` 唤醒
3. **挂起**：`suspend()` - 需要 `resume()` 恢复（已过时）

---

## 三、线程优先级

### 3.1 优先级范围

|常量|值|说明|
|---|---|---|
|`MIN_PRIORITY`|1|最低优先级|
|`NORM_PRIORITY`|5|默认优先级|
|`MAX_PRIORITY`|10|最高优先级|

### 3.2 设置优先级

```java
Thread thread = new Thread();
thread.setPriority(Thread.MAX_PRIORITY);  // 设置为最高优先级
```

**注意**：

- ⚠️ 优先级不能保证执行顺序
- ⚠️ 依赖于操作系统的调度策略
- ⚠️ 高优先级线程更可能先执行，但不是绝对的

---

## 四、创建线程的三种方式

### 4.1 方式一：实现 Runnable 接口

#### 步骤

1. 创建类实现 `Runnable` 接口
2. 重写 `run()` 方法
3. 创建 `Thread` 对象，传入 `Runnable` 实例
4. 调用 `start()` 方法启动线程

#### 示例

```java
// 创建一个新的线程
class NewThread implements Runnable {
    Thread t;
    
    NewThread() {
        // 创建线程对象
        t = new Thread(this, "Demo Thread");
        System.out.println("Child thread: " + t);
        t.start();  // 启动线程
    }
    
    // 线程执行体
    public void run() {
        try {
            for (int i = 5; i > 0; i--) {
                System.out.println("Child Thread: " + i);
                Thread.sleep(50);  // 暂停 50 毫秒
            }
        } catch (InterruptedException e) {
            System.out.println("Child interrupted.");
        }
        System.out.println("Exiting child thread.");
    }
}

public class ThreadDemo {
    public static void main(String args[]) {
        new NewThread();  // 创建并启动线程
        
        try {
            for (int i = 5; i > 0; i--) {
                System.out.println("Main Thread: " + i);
                Thread.sleep(100);
            }
        } catch (InterruptedException e) {
            System.out.println("Main thread interrupted.");
        }
        System.out.println("Main thread exiting.");
    }
}
```

**输出示例：**

```
Child thread: Thread[Demo Thread,5,main]
Main Thread: 5
Child Thread: 5
Child Thread: 4
Main Thread: 4
Child Thread: 3
Child Thread: 2
Main Thread: 3
Child Thread: 1
Exiting child thread.
Main Thread: 2
Main Thread: 1
Main thread exiting.
```

### 4.2 方式二：继承 Thread 类

#### 步骤

1. 创建类继承 `Thread` 类
2. 重写 `run()` 方法
3. 创建子类对象
4. 调用 `start()` 方法启动线程

#### 示例

```java
// 通过继承 Thread 创建线程
class NewThread extends Thread {
    NewThread() {
        // 调用父类构造器
        super("Demo Thread");
        System.out.println("Child thread: " + this);
        start();  // 启动线程
    }
    
    // 线程执行体
    public void run() {
        try {
            for (int i = 5; i > 0; i--) {
                System.out.println("Child Thread: " + i);
                Thread.sleep(50);
            }
        } catch (InterruptedException e) {
            System.out.println("Child interrupted.");
        }
        System.out.println("Exiting child thread.");
    }
}

public class ExtendThread {
    public static void main(String args[]) {
        new NewThread();  // 创建并启动线程
        
        try {
            for (int i = 5; i > 0; i--) {
                System.out.println("Main Thread: " + i);
                Thread.sleep(100);
            }
        } catch (InterruptedException e) {
            System.out.println("Main thread interrupted.");
        }
        System.out.println("Main thread exiting.");
    }
}
```

### 4.3 方式三：Callable 和 Future

#### 特点

- ✅ 可以有返回值
- ✅ 可以抛出异常
- ✅ 支持泛型

#### 步骤

1. 创建类实现 `Callable<T>` 接口
2. 重写 `call()` 方法（有返回值）
3. 使用 `FutureTask` 包装 `Callable` 对象
4. 创建 `Thread` 对象，传入 `FutureTask`
5. 调用 `start()` 启动线程
6. 调用 `FutureTask.get()` 获取返回值

#### 示例

```java
import java.util.concurrent.*;

public class CallableThreadTest implements Callable<Integer> {
    
    public static void main(String[] args) {
        CallableThreadTest ctt = new CallableThreadTest();
        FutureTask<Integer> ft = new FutureTask<>(ctt);
        
        for (int i = 0; i < 100; i++) {
            System.out.println(Thread.currentThread().getName() + 
                " 的循环变量i的值" + i);
            
            if (i == 20) {
                new Thread(ft, "有返回值的线程").start();
            }
        }
        
        try {
            System.out.println("子线程的返回值：" + ft.get());
        } catch (InterruptedException | ExecutionException e) {
            e.printStackTrace();
        }
    }
    
    @Override
    public Integer call() throws Exception {
        int i = 0;
        for (; i < 100; i++) {
            System.out.println(Thread.currentThread().getName() + " " + i);
        }
        return i;
    }
}
```

### 4.4 三种方式对比

|特性|Runnable|Thread|Callable|
|---|---|---|---|
|继承性|可继承其他类|不能继承其他类|可继承其他类|
|返回值|无|无|有|
|异常处理|不能抛出|不能抛出|可以抛出|
|代码复用|好|一般|好|
|推荐度|⭐⭐⭐⭐⭐|⭐⭐⭐|⭐⭐⭐⭐|

---

## 五、Thread 类的常用方法

### 5.1 实例方法

|方法|描述|
|---|---|
|`void start()`|启动线程，JVM 调用 run() 方法|
|`void run()`|线程执行体|
|`void setName(String name)`|设置线程名称|
|`void setPriority(int priority)`|设置优先级|
|`void setDaemon(boolean on)`|设置为守护线程|
|`void join(long millis)`|等待线程终止|
|`void interrupt()`|中断线程|
|`boolean isAlive()`|测试线程是否活动|

### 5.2 静态方法

|方法|描述|
|---|---|
|`static void yield()`|暂停当前线程，执行其他线程|
|`static void sleep(long millis)`|让当前线程休眠指定时间|
|`static Thread currentThread()`|返回当前正在执行的线程|
|`static boolean holdsLock(Object obj)`|判断是否持有对象锁|
|`static void dumpStack()`|打印堆栈跟踪|

### 5.3 常用方法示例

```java
public class ThreadMethodsDemo {
    public static void main(String[] args) throws InterruptedException {
        // 创建线程
        Thread thread = new Thread(() -> {
            System.out.println("Thread name: " + Thread.currentThread().getName());
            System.out.println("Thread priority: " + Thread.currentThread().getPriority());
            
            for (int i = 0; i < 5; i++) {
                System.out.println(i);
                try {
                    Thread.sleep(1000);  // 休眠 1 秒
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        });
        
        // 设置线程属性
        thread.setName("MyThread");
        thread.setPriority(Thread.MAX_PRIORITY);
        thread.setDaemon(false);  // 用户线程
        
        // 启动线程
        thread.start();
        
        // 等待线程结束
        thread.join();
        
        System.out.println("Thread is alive: " + thread.isAlive());
    }
}
```

---

## 六、守护线程 vs 用户线程

### 6.1 区别

|类型|说明|特点|
|---|---|---|
|**用户线程**|普通线程|JVM 等待所有用户线程结束才退出|
|**守护线程**|后台服务线程|所有用户线程结束后自动终止|

### 6.2 示例

```java
public class DaemonThreadDemo {
    public static void main(String[] args) {
        Thread daemonThread = new Thread(() -> {
            while (true) {
                System.out.println("Daemon thread running...");
                try {
                    Thread.sleep(1000);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        });
        
        // 设置为守护线程（必须在 start() 之前）
        daemonThread.setDaemon(true);
        daemonThread.start();
        
        // 主线程休眠 3 秒后结束
        try {
            Thread.sleep(3000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        
        System.out.println("Main thread exiting...");
        // 主线程结束后，守护线程也会自动终止
    }
}
```

---

## 七、线程同步

### 7.1 为什么需要同步？

多个线程访问共享资源时，可能出现数据不一致的问题。

### 7.2 同步方法

```java
public class SynchronizedMethodDemo {
    private int count = 0;
    
    // 同步方法
    public synchronized void increment() {
        count++;
    }
    
    public synchronized int getCount() {
        return count;
    }
}
```

### 7.3 同步代码块

```java
public class SynchronizedBlockDemo {
    private int count = 0;
    private Object lock = new Object();
    
    public void increment() {
        // 同步代码块
        synchronized (lock) {
            count++;
        }
    }
}
```

### 7.4 完整示例

```java
class Counter {
    private int count = 0;
    
    public synchronized void increment() {
        count++;
    }
    
    public int getCount() {
        return count;
    }
}

public class SyncDemo {
    public static void main(String[] args) throws InterruptedException {
        Counter counter = new Counter();
        
        // 创建 10 个线程，每个线程增加 1000 次
        Thread[] threads = new Thread[10];
        for (int i = 0; i < 10; i++) {
            threads[i] = new Thread(() -> {
                for (int j = 0; j < 1000; j++) {
                    counter.increment();
                }
            });
            threads[i].start();
        }
        
        // 等待所有线程结束
        for (Thread thread : threads) {
            thread.join();
        }
        
        System.out.println("Final count: " + counter.getCount());  // 10000
    }
}
```

---

## 八、线程间通信

### 8.1 wait() 和 notify()

|方法|描述|
|---|---|
|`wait()`|释放锁，进入等待状态|
|`notify()`|唤醒一个等待的线程|
|`notifyAll()`|唤醒所有等待的线程|

### 8.2 生产者-消费者示例

```java
class SharedResource {
    private int data;
    private boolean available = false;
    
    public synchronized void produce(int value) {
        while (available) {
            try {
                wait();  // 等待消费者消费
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
        
        data = value;
        available = true;
        System.out.println("Produced: " + value);
        notify();  // 唤醒消费者
    }
    
    public synchronized int consume() {
        while (!available) {
            try {
                wait();  // 等待生产者生产
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
        
        available = false;
        System.out.println("Consumed: " + data);
        notify();  // 唤醒生产者
        return data;
    }
}

public class ProducerConsumerDemo {
    public static void main(String[] args) {
        SharedResource resource = new SharedResource();
        
        // 生产者线程
        Thread producer = new Thread(() -> {
            for (int i = 0; i < 5; i++) {
                resource.produce(i);
            }
        });
        
        // 消费者线程
        Thread consumer = new Thread(() -> {
            for (int i = 0; i < 5; i++) {
                resource.consume();
            }
        });
        
        producer.start();
        consumer.start();
    }
}
```

---

## 九、线程死锁

### 9.1 什么是死锁？

两个或多个线程互相等待对方释放资源，导致永久阻塞。

### 9.2 死锁示例

```java
public class DeadlockDemo {
    private static Object lock1 = new Object();
    private static Object lock2 = new Object();
    
    public static void main(String[] args) {
        Thread thread1 = new Thread(() -> {
            synchronized (lock1) {
                System.out.println("Thread 1: Holding lock 1...");
                try {
                    Thread.sleep(100);
                } catch (InterruptedException e) {}
                
                System.out.println("Thread 1: Waiting for lock 2...");
                synchronized (lock2) {
                    System.out.println("Thread 1: Holding lock 1 & 2");
                }
            }
        });
        
        Thread thread2 = new Thread(() -> {
            synchronized (lock2) {
                System.out.println("Thread 2: Holding lock 2...");
                try {
                    Thread.sleep(100);
                } catch (InterruptedException e) {}
                
                System.out.println("Thread 2: Waiting for lock 1...");
                synchronized (lock1) {
                    System.out.println("Thread 2: Holding lock 1 & 2");
                }
            }
        });
        
        thread1.start();
        thread2.start();
    }
}
```

### 9.3 避免死锁

1. **按顺序获取锁**
2. **设置超时时间**
3. **使用 tryLock()**
4. **避免嵌套锁**

---

## 十、重点总结

### 10.1 核心概念

1. **线程生命周期**：新建 → 就绪 → 运行 → 阻塞 → 死亡
2. **创建方式**：Runnable、Thread、Callable
3. **线程同步**：synchronized 关键字
4. **线程通信**：wait()、notify()、notifyAll()

### 10.2 最佳实践

|实践|说明|
|---|---|
|优先使用 Runnable|保持继承灵活性|
|合理设置优先级|不要过度依赖|
|及时释放资源|避免内存泄漏|
|避免死锁|按顺序获取锁|
|控制线程数量|避免上下文切换开销|

### 10.3 记忆口诀

```
多线程并发执行，充分利用 CPU
三种创建方式，Runnable 最推荐
生命周期五态，新建就绪运行阻塞死亡
synchronized 同步，wait notify 通信
避免死锁发生，按序获取锁资源
合理控制数量，上下文切换有开销
```

### 10.4 注意事项

- ⚠️ 不要创建过多线程
- ⚠️ 注意线程安全问题
- ⚠️ 避免死锁
- ⚠️ 合理使用同步
- ⚠️ 守护线程会随主线程结束