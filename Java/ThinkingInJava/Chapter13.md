# 第十三章 字符串

## 13.1 不可变String

String对象是不可变的。String类中每个看起来会修改String的的方法，实际上都是创建了一个全新的String对象，以包含修改后的字符串内容，而最初的String对象则丝毫未动。

## 13.2 重载“+”与StringBuilder

不可变性会带来一些效率问题。为String对象重载的“+”操作符就是一个例子。

在使用字符串时，如果较多的使用“+”，如`String str1 = "1" + "2 " + "3" + "4"`，编译器会自动的使用`StringBuilder`来进行处理，而不是创建多个String对象，因为这样更高效。

当为一个类编写toString()方法时，如果字符串操作比较简单，就可以信赖编译器，因为它会合理的构造最终的字符串结果。但是如果要在toString()方法中使用循环，那么最好自己创建一个StringBuilder对象，用它来构造最终的结果。

## 13.5 格式化输出

### 13.5.2 System.out.format()

### 13.5.3 Formatter类

Java中，所有新的格式化功能都由java.util.Formatter类处理。可以将Formatter看作一个翻译器，它将你的格式化字符串与数据翻译成需要的结果。

### 13.5.6 String.format()

String.format()是一个static方法，它接受与Formatter.format()方法一样的参数，但是返回一个String对象。例如：

```java
public class DatabaseException extends Exception {
  public DatabaseException(int transactionID, int queryID, String message) {
    super(String.format("(t%d, q%d) %s", transactionID, queryID, message));
  }
}
```

其实在String.format()内部，它也是创建一个Formatter对象，然后将传入的参数转给该Formatter。

## 13.6 正则表达式

**粗略看一遍，有需要再详细了解**

