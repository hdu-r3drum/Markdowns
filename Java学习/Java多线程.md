# 什么是多线程？

## 进程

#### **定义**

进程是 **程序的执行实例**，是操作系统分配资源（CPU、内存、文件等）的基本单位。
每个进程拥有独立的内存空间，彼此隔离，互不干扰。

#### **特点**

- **独立性**：进程间数据不共享（需通过 IPC 通信，如管道、Socket）。
- **资源开销大**：创建和销毁进程需要分配或回收内存、文件句柄等资源。
- **安全性高**：一个进程崩溃不会直接影响其他进程。

#### **示例**

- 浏览器中每个标签页通常是一个独立进程（如 Chrome 的多进程架构）。
- 同时运行的多个程序（如 Word 和 Excel）。

## 线程

#### **定义**

线程是 **进程内的执行单元**，共享进程的资源（内存、文件等），是 CPU 调度的基本单位。

#### **特点**

- **共享资源**：同一进程的线程共享堆内存、全局变量等。
- **轻量级**：创建和切换线程的代价远低于进程。
- **同步问题**：需通过锁（如 `synchronized`）避免数据竞争（Race Condition）。

#### **示例**

- 下载工具同时下载多个文件（每个下载任务是一个线程）。
- 游戏中的后台加载资源和实时渲染可能由不同线程处理。

------

## **进程 vs 线程：核心区别**

| **特性**          | **进程**                       | **线程**                               |
| :---------------- | :----------------------------- | :------------------------------------- |
| **资源分配**      | 独立内存空间，资源开销大       | 共享进程内存，资源开销小               |
| **通信方式**      | IPC（管道、消息队列等）        | 直接读写共享内存（需同步）             |
| **创建/销毁速度** | 慢                             | 快                                     |
| **容错性**        | 高（一个进程崩溃不影响其他）   | 低（一个线程崩溃可能导致整个进程退出） |
| **适用场景**      | 需要隔离的任务（如浏览器标签） | 需要高效协作的任务（如并发计算）       |

------

### **实际应用场景**

#### **进程的典型用途**

- **高隔离性需求**：如浏览器标签（防止一个页面崩溃导致整个浏览器退出）。
- **多程序并发运行**：如同时运行 IDE 和数据库服务。

#### **线程的典型用途**

- **高并发任务**：如 Web 服务器（每个请求由一个线程处理）。
- **异步操作**：如 GUI 程序保持界面响应（主线程处理 UI，后台线程执行耗时任务）。

------

# 多线程的实现方式

### **1. 继承 `Thread` 类**

通过继承 `Thread` 类并重写 `run()` 方法实现多线程。
**适用场景**：简单任务，但 **不推荐**（Java 不支持多继承，灵活性差）。

java

复制

```
public class MyThread extends Thread {
    @Override
    public void run() {
        System.out.println("线程执行: " + Thread.currentThread().getName());
    }

    public static void main(String[] args) {
        MyThread thread1 = new MyThread();
        MyThread thread2 = new MyThread();
        thread1.start(); // 启动线程
        thread2.start();
    }
}
```

------

### **2. 实现 `Runnable` 接口（推荐）**

实现 `Runnable` 接口并重写 `run()` 方法，通过 `Thread` 类包装执行。
**优点**：避免单继承限制，适合资源共享。

java

复制

```
public class MyRunnable implements Runnable {
    @Override
    public void run() {
        System.out.println("线程执行: " + Thread.currentThread().getName());
    }

    public static void main(String[] args) {
        MyRunnable task = new MyRunnable();
        Thread thread1 = new Thread(task);
        Thread thread2 = new Thread(task);
        thread1.start();
        thread2.start();
    }
}
```

------

### **3. 实现 `Callable` 接口 + `FutureTask`**

通过 `Callable` 实现有返回值的线程任务，结合 `FutureTask` 获取结果。
**适用场景**：需要异步计算结果或捕获异常。

java

复制

```
import java.util.concurrent.Callable;
import java.util.concurrent.FutureTask;

public class MyCallable implements Callable<String> {
    @Override
    public String call() throws Exception {
        return "任务结果: " + Thread.currentThread().getName();
    }

    public static void main(String[] args) throws Exception {
        FutureTask<String> futureTask = new FutureTask<>(new MyCallable());
        Thread thread = new Thread(futureTask);
        thread.start();
        System.out.println(futureTask.get()); // 阻塞获取结果
    }
}
```

------

### **4. 使用线程池（`ExecutorService`）**

通过线程池管理线程生命周期，提高资源复用效率。
**优点**：避免频繁创建/销毁线程，支持任务队列和线程控制。

java

复制

```
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;

public class ThreadPoolExample {
    public static void main(String[] args) {
        ExecutorService executor = Executors.newFixedThreadPool(3); // 固定大小线程池
        
        for (int i = 0; i < 5; i++) {
            executor.execute(() -> {
                System.out.println("线程执行: " + Thread.currentThread().getName());
            });
        }
        
        executor.shutdown(); // 关闭线程池
    }
}
```

------

### **5. 使用 `CompletableFuture`（Java 8+）**

简化异步编程，支持链式调用和组合多个异步任务。
**适用场景**：复杂异步任务编排。

java

复制

```
import java.util.concurrent.CompletableFuture;

public class CompletableFutureExample {
    public static void main(String[] args) {
        CompletableFuture.runAsync(() -> {
            System.out.println("异步任务: " + Thread.currentThread().getName());
        }).thenRun(() -> {
            System.out.println("后续操作");
        });
    }
}
```

------

### **6. 定时任务线程池（`ScheduledExecutorService`）**

支持定时或周期性执行任务。

java

复制

```
import java.util.concurrent.Executors;
import java.util.concurrent.ScheduledExecutorService;
import java.util.concurrent.TimeUnit;

public class ScheduledThreadPoolExample {
    public static void main(String[] args) {
        ScheduledExecutorService scheduler = Executors.newScheduledThreadPool(1);
        
        // 延迟 2 秒后执行
        scheduler.schedule(() -> {
            System.out.println("延迟任务执行");
        }, 2, TimeUnit.SECONDS);
        
        // 每隔 3 秒周期性执行
        scheduler.scheduleAtFixedRate(() -> {
            System.out.println("周期性任务");
        }, 0, 3, TimeUnit.SECONDS);
    }
}
```

------

### **方法对比**

| **方法**              | **优点**               | **缺点**                   | **适用场景**         |
| :-------------------- | :--------------------- | :------------------------- | :------------------- |
| 继承 `Thread`         | 简单直接               | 无法继承其他类，灵活性差   | 简单测试             |
| 实现 `Runnable`       | 支持多实现，资源共享   | 无返回值                   | 大多数多线程场景     |
| `Callable` + `Future` | 支持返回值和异常       | 需处理 `Future` 的阻塞问题 | 需要异步结果的任务   |
| 线程池                | 资源复用，高效管理线程 | 需合理配置参数             | 高并发或长期运行任务 |
| `CompletableFuture`   | 支持链式调用和组合任务 | 需理解函数式编程           | 复杂异步任务编排     |
| 定时任务线程池        | 支持定时/周期性任务    | 不适用于一次性任务         | 定时任务调度         |

------

### **最佳实践**

1. **优先选择 `Runnable` 或线程池**：避免资源浪费，提升性能。
2. **避免直接操作 `Thread` 类**：使用线程池统一管理。
3. **处理线程安全**：对共享资源使用 `synchronized` 或 `Lock`。
4. **合理配置线程池参数**：根据任务类型（CPU 密集型/IO 密集型）设置线程数。

通过选择合适的多线程实现方式，可以显著提升程序的并发性能和可维护性。

# 生命周期

# 竞态条件

## 同步代码块

## 同步方法

- 锁住方法里面的所有方法
- 锁对象不能指定自己

# 死锁

两个锁嵌套

## 死锁的必要条件

- 互斥条件：每个资源要么被一个进程独占，要么就空闲。
- 占有并等待条件：已经获得了一些资源的进程会申请其他资源。
- 不可剥夺条件：已经分配给进程的资源不能被其他进程抢占，只能由该进程自己释放。
- 循环等待条件：系统必须存在一个由两个或两个以上进程组成的循环，每个进程都在等待下一个进程所占用的资源

# 线程池

## 核心线程和临时线程

### **一、核心线程 vs 临时线程**

| **特性**     | **核心线程**                                                 | **临时线程**                                     |
| :----------- | :----------------------------------------------------------- | :----------------------------------------------- |
| **存活机制** | 默认长期存活，即使空闲也不销毁（除非设置 `allowCoreThreadTimeOut=true`） | 空闲超过 `keepAliveTime` 后自动销毁              |
| **数量控制** | 由 `corePoolSize` 参数指定                                   | 最大数量由 `maximumPoolSize - corePoolSize` 决定 |
| **创建时机** | 线程池初始化时或任务到达时提前创建（预热）                   | 当任务队列已满且核心线程忙碌时创建               |
| **用途**     | 处理常规任务流量                                             | 应对突发流量或任务高峰                           |

# volatile

在 Java 中，**`volatile`** 关键字用于解决多线程环境下的 **内存可见性** 和 **指令重排序** 问题。它通过强制线程直接从主内存读写变量，并插入内存屏障（Memory Barrier）来保证操作的有序性。以下是其核心原理、作用及使用场景的详细解析：

------

### **一、`volatile` 的三大特性**

| **特性**         | **说明**                                                     |
| :--------------- | :----------------------------------------------------------- |
| **可见性**       | 线程对 `volatile` 变量的修改对其他线程立即可见，避免缓存不一致问题。 |
| **有序性**       | 禁止 JVM 和处理器对 `volatile` 变量的读写操作进行指令重排序。 |
| **原子性不保证** | `volatile` **不保证复合操作的原子性**（如 `i++`），需配合锁或原子类。 |

------

### **二、底层实现原理**

#### **1. 内存屏障（Memory Barrier）**

JVM 通过插入内存屏障指令实现 `volatile` 的语义：

- **写屏障（Store Barrier）**：
  确保 `volatile` 写操作前的所有普通写操作对其他线程可见。
- **读屏障（Load Barrier）**：
  确保 `volatile` 读操作后的所有普通读操作从主内存加载最新值。

#### **2. 缓存一致性协议（如 MESI）**

CPU 通过监听总线，检测其他核心对 `volatile` 变量的修改，强制失效本地缓存并重新加载数据。

------

### **三、典型使用场景**

#### **1. 状态标志位**

通过 `volatile` 变量控制线程退出：

```java
public class WorkerThread extends Thread {
    private volatile boolean running = true;

    @Override
    public void run() {
        while (running) {
            // 执行任务
        }
    }

    public void stopWork() {
        running = false; // 其他线程修改后，工作线程立即可见
    }
}
```

#### **2. 双重检查锁定（Double-Checked Locking）**

单例模式中避免指令重排序导致对象未完全初始化：

```java
public class Singleton {
    private static volatile Singleton instance;

    public static Singleton getInstance() {
        if (instance == null) { // 第一次检查
            synchronized (Singleton.class) {
                if (instance == null) { // 第二次检查
                    instance = new Singleton(); // 避免指令重排序
                }
            }
        }
        return instance;
    }
}
```

**未加 `volatile` 的风险**：
JVM 可能将对象初始化过程重排序为：分配内存 → 返回引用 → 初始化对象，导致其他线程获取到未初始化的实例。

------

### **四、`volatile` vs `synchronized`**

| **对比项**   | **`volatile`**          | **`synchronized`**       |
| :----------- | :---------------------- | :----------------------- |
| **作用范围** | 变量级别                | 代码块或方法级别         |
| **可见性**   | 保证                    | 保证                     |
| **原子性**   | 不保证（单次读/写原子） | 保证                     |
| **有序性**   | 限制指令重排序          | 通过锁保证临界区串行执行 |
| **性能开销** | 低（无锁）              | 高（上下文切换、锁竞争） |

------

### **五、常见误区**

#### **1. `volatile` 不适用于复合操作**

如 `i++` 实际上是 `读取 → 修改 → 写入` 的复合操作，需用 `AtomicInteger` 或锁：

```java
private volatile int count = 0;

// 错误：线程不安全
public void increment() {
    count++; // 非原子操作
}

// 正确：使用原子类
private AtomicInteger safeCount = new AtomicInteger(0);
public void safeIncrement() {
    safeCount.incrementAndGet();
}
```

#### **2. `volatile` 不能替代锁**

当操作涉及多个共享变量时，仍需使用锁保证原子性：

```java
private volatile int x = 0;
private volatile int y = 0;

// 错误：x 和 y 的更新可能被其他线程打断
public void update(int a, int b) {
    x = a;
    y = b;
}

// 正确：使用锁
public synchronized void safeUpdate(int a, int b) {
    x = a;
    y = b;
}
```
