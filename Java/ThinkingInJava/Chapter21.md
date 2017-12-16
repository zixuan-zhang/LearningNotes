# 第21章 并发

### 21.1.1 更快的执行

并发通常是提高运行在单处理器上的程序的性能

在单处理器上运行的并发程序开销应该比程序的所有部分都顺序执行的开销大，因为其中增加了上下文切换的代价。然而，使这个问题有些不同的是阻塞，如果程序中的某个任务因为该程序控制范围之外的某些条件（通常是IO）而导致不能继续执行，那么我们就说这个任务被阻塞了。如果使用并发，当一个任务阻塞时，程序中的其他任务还可以继续执行。

Java的线程机制是抢占式的，这表示调度机制会周期性的中段线程，将上下文切换到另一个县城，从而为每个线程提供时间片，使得每个线程都会分配到河里的时间去任务。在协作式系统中，每个任务都会自动地放弃控制，这要求程序猿要有意识地在每个任务中插入某种类型的让步语句。

## 21.2 基本的线程机制

一个线程就是在进程中的单一的顺序控制流，因此，单个进行可以拥有多个并发的任务。

### 21.2.1 定义任务

线程可以驱动任务，因此你需要一种描述任务的方式，这可以由Runnable接口来提供。要想定义任务，只需要实现`Runnable`接口并编写`run()`方法，使得该任务可以执行命令。

```java
public class ListOff implements Runnable {
  protected int countDown = 10;
  private static int taskCount = 0;
  private final int id = taskCount++;
  public ListOff() {}
  public ListOff(int countDown) {
    this.countDown = countDown;
  }
  public String status() {
    return "#" + id;
  }
  public void run() {
    while (countDown-- > 0) {
      System.out.print(status());
      Thred.yield();
    }
  }
}
```

任务的`run()`方法通常会有某种形式的循环，使得任务一直运行下去直到不再需要，所以要设定跳出循环的条件（有一种选择直接从`run()`返回），通常，`run()`写成无限循环的形式，这就意味着，除非有某个条件使得run()终止，否则它将永远运行下去。

在`run()`中对静态方法`Thread.yield()`调用时对线程调度器的一种建议，它意味着“我已经执行完声明周期中最重要的一部分了，此刻正是切换给其他任务执行一段时间的大好时机”

`Runnable`类并不会产生任何内在的线程能力，要实现线程行为，必须显示的将任务附着到线程上。

###  21.2.2 Thread类

将`Runnable`对象变为工作任务的传统方式就是把它交给一个Thread构造器。

```java
public class BasicThread {
  public static void main(String[] args) {
    Thread t = new Thread(new LiftOff());
    t.start();
  }
}
```

`Thread`构造器只需要一个`Runnable`对象，调用`start()`方法为该线程执行必须的初始化操作。`start()`方法产生了一个对长期运行方法的调用，但是`start()`迅速返回了。

### 21.2.3 使用Executor

执行器`Executor`将管理`Thread`对象，从而简化了并发编程。`Executor`在客户端和任务执行之间提供了一个间接层。`Executor`允许你管理异步任务的执行，而无需显示的管理线程的生命周期。

```java
public class CachedThreadPool {
  public static void main(String[] args) {
    ExecutorService exec = Executors.newCachedThreadPool();
    for (int i = 0; i < 5; ++i) {
      exec.execute(new LiftOff());
    }
    exec.shutdown();
  }
}
```

对`shutdown()`方法的调用可以防止新任务被提交给这个`Executor`，当前线程（即`main()`线程）将继续运行在`shutdown()`被调用之前提交的所有任务。这个程序将在`Executor`中所有任务完成后尽快推出。

`Executors.newFixedThreadPool(THREAD_NUM)`使用有限的线程来执行所提交的任务。

`CachedThreadPool`在程序执行过程中通常会创建与所需数量相同的线程，然后在它回收线程时停止创建新线程，因此它时合理的`Executor`首选。只有当这种方式引发问题时，才需要切换到`FixedThreadPool`。

### 21.2.4 从任务中产生返回值

`Runnable`是执行工作的独立任务，但是它不反悔返回值。如果你希望任务在完成时返回一个值，可以实现`Callable`接口而不是`Runnable`接口。`Callable`时一个具有类型参数的泛型，它的类型参数表示的是从方法`call()`中返回的值，而且必须使用`ExecutorService.submit()`方法调用：

```java
class TaskWithResult implement Callable<String> {
  private int it;
  public TaskWithResult(int id) {
    this.id = id;
  }
  public String call() {
    return "result of TaskWithResult " + id;
  }
}
public class CallableDemo {
  public static void main(String[] args) {
    ExecutorService exec = Executors.newCachedThreadPool();
    ArrayList<Future<String>> results = new ArrayList<Future<String>>();
    for (int i = 0; i < 10; i++) {
      results.add(exec.submit(new TaskWithResult(i)));
    }
    for (Future<String> fs : results) {
      try {
        // get() blocks until completion
        System.out.println(fs.get());
      }
    }
  }
}
```

`submit()`方法会产生`Future`对象，它用`Callable`返回结果的特定类型进行了参数化。你可以用`isDone()`方法来查询`Future`是否已经完成。当任务完成时，可以调用`get()`方法获取该结果。

### 21.2.5 休眠

`Thread.sleep(5000)`

### 21.2.6 优先级

线程的优先级将该线程的重要性传递给 了调度器。尽管CPU处理现有线程集的顺序是不确定的，但是调度器将倾向于让优先级高的线程线执行。然而，这不意味着优先级较低的线程得不到执行，只是执行的频率较低。

大多数情况下，所有线程应该以默认优先级运行。试图操作线程优先级通常是一种错误。

### 21.2.8 后台线程

所谓后台（daemon）线程，是指在程序运行的时候在后台提供一种通用服务的线程，并且这种线程不属于程序中不可缺少的部分。因此当所有的非后台线程结束时，程序也就终止了，同时会杀死进程中所有后台线程。反过来说，只要有任何非后台线程还在运行，程序就不会终止。

Daemon的作用是为其他线程的运行提供便利服务，守护线程最典型的应用就是 GC (垃圾回收器)，它就是一个很称职的守护者。

### 21.2.11 加入一个线程

一个线程可以在其他线程之上调用`join()`方法，其效果时等待一段时间直到第二个线程结束才继续执行。也可以在调用`join()`时带上一个超时参数，如果目标线程在这段时间到期时还没有结束的话，`join()`方法总能返回。

## 21.3 共享受限资源

### 21.3.2 解决共享资源竞争

防止冲突的方法就是当资源被一个任务使用时，在其上加锁。第一个访问某项资源的任务必须锁定这项资源，使其他任务在其被解锁前，就无法访问他，而在解锁时，另一个任务就可以锁定并使用它。

基本上所有的并发模式在解决线程冲突问题的时候，都采用序列化访问共享资源的方案。在给定时刻只允许一个任务访问共享资源。通常这通过在代码前加上一个锁语句来实现的，使得在一段时间内只有一个任务可以运行这段代码。因为锁语句产生了一种互斥的效果，所以这种机制被称为互斥量(mutex)。

Java提供关键字`synchronized`，为防止资源冲突提供了内置支持。当任务要执行被`synchronized`关键字保护的代码片段的时候，它将检查锁是否可用，然后获取锁，执行代码，释放锁。

### 21.3.3 原子性与易变性

原子性可以应用于除long和double外的所有基本类型之上的“简单操作”。对于读取和写入除long和double之外基本类型变量这样的操作，可以保证它们会被当作不可分（原子）的操作来操作内存。

`volatile`关键字确保了应用中的可视性。如果将一个域被声明为volatile的，那么只要对这个域产生了写操作，那么所有的读操作就可以看到这个修改。即便是用了本地缓存，volatile域会立即被写入到内存中，而读内存发生在主存中。

### 21.3.4 原子类

有`AtomicInteger, AtomicLong, AtomicReference`等特殊的原子性变量类。

### 21.3.5 临界区

```
synchronized(syncObject) {
  // This code can be accessed by only one task at a time.
}
```

### 21.3.7 线程本地存储

防止任务在共享资源上产生冲突的第二种方式就是根除对变量的共享。线程本地存储室一种自动化的机制。如果有多个线程都要使用变量x所表示的对象，那线程本地存储就会生成5个用于x的不同的存储快。

创建和管理线程本地存储可以又`java.lang.ThreadLocal`来实现

```java
class Accessor implements Runnable {
  private final int id;
  public Accessor(int idn) { id = idn; }
  public void run() {
    while (!Thread.currentThread().isInterrupted()) {
      ThreadLocalVariableHolder.increment()
    }
  }
}
public class ThreadLocalVariableHolder {
  private static ThreadLocal<Integer> value = new ThreadLocal<Integer>() {
    private Random rand = new Random(47);
    protected synchronized Integer initialValue() {
      return rand.nextInt(10000);
    }
  };
  public static void increment() {
    value.set(value.get() + 1);
  }
  public static int get() {
    return value.get();
  }
}
```

ThreadLocal对象通常当作静态域存储。在创建ThreadLocal时，只能通过get()和set()方法来访问该对象的内容，其中get()方法将返回其与线程相关联对象的副本，而set()会将参数插入到为其线程存储的对象中，并返回存储中原有的对象。

## 21.4 终结任务

### 21.4.2 在阻塞时终结

**进入阻塞状态**

一个任务进入阻塞状态，可能有如下原因：

1. 通过调用`sleep()`使任务进入休眠状态。
2. 通过调用`wait()`使线程刮起，直到得到了`notify()`或`notifyAll()`消息。或者`signal()`或`signalAll()`。就会进入就绪状态。
3. 任务在等待某个输入、输出完成
4. 任务在某个对象上调用其同步控制方法，但是对象锁不可用，因为已经被另一个任务获取。

### 21.4.3 中断

还需要更多知识。。。

