# 第十二章 通过异常处理错误

发现错误的理想时机是在编译阶段，也就是在试图运行程序之前。然而，编译期间不能找出所有的错误，余下的问题必须在运行期解决。这就需要错误源能通过某种方式，把适当的信息传递给某个接受者——它知道如何正确处理这个问题。

Java使用异常来提供一致的错误报告了警，使得构件能够与客户端代码可靠的沟通问题。

## 12.6 捕获所有异常

Exception类是与编程有关的所有异常类的基类，所以它不包含太多具体的信息，不过可以调用它从基类Throwable继承的方法：

* String getMessage()
* String getLocalizedMessage()

来获取详细信息，或用本地语言表示的详细信息

* String toString()

返回队Throwable的简单描述

* void printStackTrace()
* void printStackTrace(PrintStream)
* void printStackTrace(java.io.PrintWriter)

打印Throwable和Throwable的调用栈轨迹。后两个版本允许选择要输出的流。

* Throwable fillInStackTrace()

用于在Throwable对象的内部记录栈帧的当前状态。这在程序重新抛出异常或错误时非常有用。

## 12.7

只能在代码中忽略RuntimeException（及其子类）类型的异常，其他类型异常的处理都是由编译期强制实施的。究其原因，RuntimeException代表的是编程错误：

1. 无法预料的错误。比如从你控制范围外传递进来null引用
2. 作为程序员，应该在代码中检查的错误。

## 12.8 Finally

### 12.8.2 在return中使用finally

因为finally子句总是会被执行，所以在一个方法中，可以从多个点返回，并且可以保证重要的清理工作人会执行：

```java
public class MultipleReturns {
  public static void f(int i) {
    print("Initialization that requires cleanup");
    try {
      print("Point 1");
      if (i == 1) return;
      if (i == 2) return;
      if (i == 3) return;
    } finally {
      print("Performing cleanup");
    }    
  }
}
```

## 12.9 异常的限制

当覆盖方法的时候，只能抛出在基类方法的异常说明里那些列出来的异常。这个限制很有用，意味着，当基类使用的代码应用到其派生类对象的时候，一样能够工作。