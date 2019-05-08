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

## Item 7: Eliminate obsolete object references

## Item 8: Avoid finalizers and cleaners

## Item 9: Prefer try-with-resources to try-finally
