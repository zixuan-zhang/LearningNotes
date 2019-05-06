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

## Item 3: Enforce the singleton property with a private constructor or an enum type

## Item 4: Enforce non-instantiability with a private constructor

## Item 5: Prefer dependency injection to hardwiring resources

## Item 6: Avoid creating unnecessary objects

## Item 7: Eliminate obsolete object references

## Item 8: Avoid finalizers and cleaners

## Item 9: Prefer try-with-resources to try-finally
