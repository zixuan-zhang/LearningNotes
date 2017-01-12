## 异常处理

Java的异常处理机制是个简洁、轻量化的执行期间例外情况处理方式，它能让你能够将处理错误状况的程序代码摆在一个容易阅读的位置。不过这要依赖已经知道的所调用的方法是有风险的，因此可以编写出处理此刻能行的程序代码。

抛出异常的语法：
```
public void takeRisk() throws BadException {
    if (abandonAllHope) {
        throw new BadException();
    }
}

public void crossFingers() {
    try {
        object.takeRisk();
    }
    catch (BadException ex) {
        System.out.println("Aaargh");
        ex.printStaskTrace();
    }
    finally {
        ...
    }
}
```
如果有抛出异常，则一定要使用`throw`来声明这件事。如果要调用会抛出异常的方法，则必须知道异常的可能性。将调用包在`try/catch`块中是满足编译器的方法。

`finally`块是用来存放不管有没有异常都会执行的程序。
