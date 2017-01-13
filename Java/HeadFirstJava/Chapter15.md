## 网络与线程

### 客户端
```
import java.io.*;
import java.net.*;

public class DailyAdviceClient {
    public static void main(String[] args) {
        DailyAdviceClient client = new DailyAdviceClient();
        client.go();
    }

    private void go() {
        try {
            Socket s = new Socket("127.0.0.1", 4242);
            InputStreamReader streamReader = new InputStreamReader(s.getInputStream());
            BufferedReader reader = new BufferedReader(streamReader);

            Streing advice = reader.readLine();
            System.out.println("Today you should : " + advice);
            reader.close();
        } catch (IOException ex) {
            ex.printStackTrace();
        }
    }
}
```

### 服务器
```
ServerSocket serverSock = new ServerSocket(4242); // 监听来自4242端口的消息
Socket sock = serverSock.accept();
PrintWriter writer = new PrintWriter(sock.getOutputStream()); writer.println("something");
writer.close();
```

### 多线程
如何创建多线程：
1. 建立Runnable对象：`Runnable threadJob = new MyRunnable();` 需要编写并实现`Runnable`类，而此类就是对线程要执行任务的定义。
2. 建立`Thread`对象并赋值`Runnable`任务：`Thread myThread = new Thread(threadJob);`把`Runnalbe`对象传给Thread的构造函数。这会告诉Thread对象把那个方法放在执行空间里去执行——Runnable的run方法。
3. 启动Thread: `myThread.start();`

线程怎么知道要写执行哪个方法？因为`Runnable`定义了一个协约。因为`Runnable`是个接口，线程的任务可以被定义在任何实现Runnable的类上。线程只关心传入给Thread类的构造函数是否为实现了Runnable的类。
```
public class MyRunnable implements Runnable {
    public void run() {
        go();
    }

    private void go () {
        // Do something
    }
}

class ThreadTest {
    public static void main(String[] args) {
        Runnable threadJob = new MyRunnable();
        Thread thread = new Thread(threadJob);
        thread.start();
    }
}
```

#### 线程的执行状态
1. 新建
2. 可执行：已经start
3. 阻塞：暂时不可执行

* Java虚拟机的线程调度机制来决定执行哪个线程。
* Thread对象不可重复使用，一旦线程的run()方法完成后，该线程就不能再重新启动。

#### 同步
* `synchronized`关键字可以防止多个线程同时进入同一对象的同一方法。
* **关于线程的同步与互斥这里讲的并不多，需要看更多的资料才会更加明白有应该怎么做**
