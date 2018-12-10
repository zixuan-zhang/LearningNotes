
# Chapter 5 单件模式 Singleton Pattern

## 演进

### Version 1
经典模式
```java
public class Singleton {
  private static Singleton instance;
  private Singleton() {};
  public static Singleton getInstance() {
    if (instance == null) {
      instance = new Singleton();
    }
    return instance;
  }
}
```

### Version 2
可处理多线程模式
```java
public class Singleton {
  private static Singleton instance;
  private Singleton() {};
  public static synchronized Singleton getInstance() {
    if (instance == null) {
      instance = new Singleton();
    }
    return instance;
  }
}
```
使用`synchronized`关键字来处理多线程访问，但是这种方式每次`getInstance()`时都会触发同步处理机制，效率可能比较慢。

### Version3
```java
public class Singleton {
  private static Singleton instance = new Singleton();
  private Singleton() {};
  public static Singleton getInstance() {
    return instance;
  }
}
```
利用这个做法，依赖JVM每次在使用该类后，都会创建静态实类，保证在多线程获取前创建成功。

### Version 4
用双重加锁，首先检查实例是否创建，如果尚未创建才进行同步，这样下来，只会有一次同步。
```java
public class Singleton {
  private volatile static Singleton instance;
  private Singleton() {};
  public Singleton getInstance() {
    if (instance == null) {
      synchronized (Singleton.class) {
        if (instance == null) {
          instance = new Singleton();
        }
      }
    }
    return instance;
  }
}
```
`volatile` 关键字确保instance被初始化时，多个线程正确处理。这种方法可以大大减少`getInstance()`性能开销。

## 问题

### 问题一
* 问：难道不能创建一个类，把所有的变量和方法都定义成静态的， 直接把类当作单例？
* 答：如果类比较小，不依赖于复杂的初始化，可以这么做。但是因为Java静态类初始化控制权在Java手里，可能导致混乱。

### 问题二
* 问：类加载器（ClassLoader)，听说两个类加载器可以创建各自的实例
* 答：是的。每个类加载器都定义了一个命名空间，如果有两个以上的类加载器，不同的类加载器可能会加载同一个类，从整个程序来看，同一个类会被加载多次。如果这样的事情发生在单例上，就会产生多个单例并存的怪异现象。所以，如果你的程序有多个类加载器，同时又使用了单例模式，请小心，有一个解决办法；自行制定类加载器，并制定同一个类加载器。

### 问题三
* 问：我想把单例当成超类，设计出子类，但是遇到了问题，究竟可不可以继承单例类
* 答：继承单例会遇到一个问题，就是构造器是私有的，不能用私有的构造器来扩展，所以你必须把单例的构造器改成公开的或受保护的，这样的话，其实就不算是单例了。如果真的把构造器的权限改了，还有另外的问题会出现。单例的实现是利用静态变量，直接继承会导致所有的派生类共享同一个实例变量，这很容易出现问题。

### 问题四
* 问：我还是不了解为啥全局变量比单例模式差
* 答：在Java中，全局变量基本上就是对对象的静态饮用，在这样的情况下使用全局变量会有一些缺点，我们已经提到了其中的就是：急切实例VS延迟实例话。但是我们要继续这个模式的目的：确保只有一个实例并提供全局访问，但是不能确保只有一个实例。全局变量也会变相鼓励开发人员，用许多全局变量指向许多很小的对象造成命名空间（namespace）的污染，单例不鼓励这样的现象，但单例仍然可能被滥用。
