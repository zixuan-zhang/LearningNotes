# Chapter 2 Creating and Destroying Objects

## Item 1: Consider static factory methods instead of constructors
例子：
```java
public static Boolean valueOf(boolean b) {
  return b ? Boolean.TRUE : Boolean.FALSE;
}
```
** 优点**
* 静态工厂方法第一个优点，它不像构造方法，它们是有名字的
* 静态工厂方法第二个优点，与构造方法不同，它们不需要每次调用都创建一个对象。因此提供了**缓存**的可能性。
* 静态工厂方法第三个优点，它可以放回其返回类型的任何子类型的对象
* 第四个优点。返回对象的类可以根据参数的不同而不同。
* 第五个优点。在编写包含该方法的类时，返回的对象的类不需要存在。

**缺点**
* 第一个缺点。没有公共或者受保护的构造方法的类不能被子类化。
* 第二个缺点。它们比较难被找到。很难找到如何实例化一个静态工厂方法而不是构造方法的类。

**命名例子**
* `from`: 类型转换方法，接受单个参数并返回此类型对应实例
* of: 一个聚合方法，接受多个参数并返回类型的实例
* valueOf: 更为详细的方法
* instance或getInstance: 返回由其参数（如果有的话）描述的实例，但不能说它具有相同的值
* create或newInstance: 该方法保证每个调用返回一个新的实例

## Item 2: Consider a builder when faced with many constructor parameters
* Builder模式非常灵活，单个builder可以重复使用来创建多个对象。builder的参数可以在构建方法的调用之间进行调整，以改变创建的对象。builder可以在创建对象时自动填充一些属性，例如每次创建对象时增加序列号。
* Builder模式也有缺点。首先必须创建它的builder，性能关键时可能会出现问题。builder模式比伸缩构造方法更加冗长，因此只有在足够的参数时才值得使用，例如四个及以上。

## Item 3: Enforce the singleton property with a private constructor or an enum type
单例对象通常表示无状态对象。记得加个private的构造器就成了。

## Item 4: Enforce non-instantiability with a private constructor
如果想要一个非实例化得类的话，比如util类，加一个private constructor

## Item 5: Prefer dependency injection to hardwiring resources
**这一条很有用，依赖注入优于硬连接资源**

许多类依赖于一个或多个底层资源。例如，拼写检查器依赖于字典。将此类实现为静态实用工具并不少见。
```java
public class SpellChecker {
  private static final Lexicon dictionary = ...;
  private SpellChecker() {}
  public static boolean isValid(String word) {}
}
```
同样的，将他们实现为单例的也不少见。
```java
public class SpellChecker {
  private final Lexicon dictionary = ...;
  private SpellChecker(...) {}
  public static INSTANCE = new SpellChecker(...);
  public boolean isValid(String word) {...}
}
```
这两种方法都不令人满意，因为他们假设只有一本字典值得使用。实际中，每种语言都有自己的字典。

因此，所需要的事能够支持类的多个实例（在示例中，即SpellChcker），每个实例都使用客户端所期望的资源（dictionary）。满足这一需求的简单模式是在创建实例时将资源传递到构造方法中。这是依赖注入（dependency injection）的一种形式：字典是检查拼写的一个依赖项，当他创建时被注入到检查拼写中）。
```java
public class SpellChecker {
  private final Lexicon dictionary;
  public SpellChecker(Lexicon dictionary) {
    this.dictionary = dictionary
  }
  public boolean isValid(String word) {...}
}
```
依赖注入模式非常简单。虽然这个例子只有一个资源，但是依赖注入可以使用任意数量的资源和任意依赖图。它保持了不变性，因此多个客户端可以共享依赖对象。依赖注入同样适用于构造方法，静态工厂和builder模式。

该模式的一个变体是将资源工厂传递给构造方法。工厂是可以重复调用以创建类型实例的对象。这种工厂体现了工厂方法模式。

尽管依赖注入极大提高了灵活性和可测试性，但是它可能使大型项目变得混乱，这些项目通常包含数千个依赖项。使用依赖注入框架（入Guice）可以消除这些混乱。

总之，不要使用单例或者静态实用类来实现一个类，该类依赖于一个或多个底层资源，这些资源的行为会影响类的行为，并且不让类直接创建这些资源。相反，将资源或工厂传递给构造方法。这种成为依赖注入的实践将极大增强类的灵活性，可重用性和可测试性。

## Item 6: Avoid creating unnecessary objects
如果对象时不可变得，总是可以重用的。

通过静态工厂方法，可以避免创建不需要的对象。如果要一个对象比较昂贵，可以缓存起来。优先使用基本类型而不是装箱的基本类型，也要注意无意识的自动装箱。

这个条目不应该被误解为暗示对象创建是昂贵的，应该避免创建对象。 相反，使用构造方法创建和回收小的对象是非常廉价，构造方法只会做很少的显示工作，尤其是在现代 JVM 实现上。 创建额外的对象以增强程序的清晰度，简单性或功能性通常是件好事。

相反，除非池中的对象非常重量级，否则通过维护自己的对象池来避免对象创建是一个坏主意。对象池的典型例子就是数据库连接。建立连接的成本非常高，因此重用这些对象是有意义的。但是，一般来说，维护自己的对象池会使代码混乱，增加内存占用，并损害性能。现代 JVM 实现具有高度优化的垃圾收集器，它们在轻量级对象上轻松胜过此类对象池。

## Item 7: Eliminate obsolete object references
一个常见的内存泄漏来源是栈数组：
```java
public Object pop() {
  if (size == 0)
    throw new EmptyStackException();
  Object result = elements[--size];
  elements[size] = null; // 要记住一定要标为null，否则数组还会对其进行引用
  return result;
}
```

另一个常见的内存泄漏原来是缓存。所以要记得清理，例如`WeakHashMap`

第三个常见的内存泄漏来源是监听器和其他回调。如果你实现了一个API，其客户端注册回调，但是没有显示的撤销注册回调，除非采取一些操作，否则他们将积累。确保回调是垃圾回收的一种方法，只存储弱引用

因为内存泄漏通常不会表现为明显的故障，所以他们会在系统中保持多年。

## Item 8: Avoid finalizers and cleaners
`Finalizer`和`Cleaner`的一个缺点是不能保证他们能够及时执行。在一个对象变得无法访问时，到`Finalizer`机制开始运行时，这个时间的任意长的。例如，依赖Finalizer机制关闭文件是严重的错，因为打开的文件描述符是有限的资源。如果由于系统迟迟没有运行而导致许多文件被打开，程序可能会失败。

及时执行Finalizer是垃圾收集算法的一个功能，这个算法在不同的视线中有很大不同。

Finalizer机制线程的优先级低于其他应用程序线程，所以对象被回收的速度低于进入队列的速度。语言规范并不保证哪个线程执行Finalizer机制，因此除了避免使用Finalizer之外，没有更好的方式来防止这类问题。在这个方面，Cleaner机制比Finalizer机制要更好一些，因为Java类的创建者可以控制自己cleaner机制的线程，但cleaner机制仍然在后台运行，在垃圾回收期的控制下运行，但不能保证及时清理。其实，Java规范并不能保证他们会运行。

Finalizer机制的另一个问题是在执行过程中，未捕获的异常会被忽略，并且该对象的Finalizer也会终止，并且不会发出警告。

使用Finalizer和Cleaner机制会导致严重的性能损失。这主要是因为Finalizer机制会阻碍有效的垃圾收集。

**其他的没看，我认为记住这些就行了**

## Item 9: Prefer try-with-resources to try-finally
Java类库中包含许多必须通过调用`close`方法手动关闭的资源。客户经常忽视关闭资源，性能会很差。

从以往看，`try-finally`是保证资源正确关闭的最佳方，即使程序抛出异常或返回。
```java
static String firstLineOfFile(String path) throw IOException {
  BufferedReader br = new BufferedReader(new FileReader(path));
  try {
    return br.readLine();
  } finally {
    br.close();
  }
}
```

当添加第二个资源时，情况会变糟：
```java
static void copy(String src, String dst) throw IOException {
  InputStream in = new FileInputStream(src);
  try {
    OutputStream out = new FileOutputStream(dst);
    try {
      byte[] buf = new byte[BUFFER_SIZE];
      int n;
      while ((n = in.read(buf)) >= 0) {
        out.write(buf, 0, n);
      }
    }finally {
      out.close();
    }
  } finally {
    in.close();
  }
}
```
这里有个微妙的缺陷。try和finally的代码都有可能抛出异常。例如`readLine()`方法由于底层物理设备发生故障，会抛出异常，然后调用`close()`函数，仍然会抛出异常。然后最后一个异常会被捕获，而`readLine()`中的异常却被flush了。这可能在实际系统中调试非常复杂——通常你会想诊断第一个异常。

java 7中引入了`try-with-reasouce`语句。要使用这个构造，资源必须实现`AutoCloseable`接口，该接口返回为void的close组成。
```java
static void copy(String src, String dst) throw IOException {
  try (InputStream in = new FileInputStream(src);
      OutputStream out = new FileOutputStream(dst)) {
        byte[] buf = new byte[BUFFER_SIZE];
        int n;
        while ((n = in.read(buf)) > 0) {
          out.write(buf, 0, n);
        }
      }
}
```
首先这个方式更加精简。而且，如果`readLine()`和`close()`都抛出异常，那么`close()`的异常会被suppress. 这个应该是try-with-source语句来实现的。
