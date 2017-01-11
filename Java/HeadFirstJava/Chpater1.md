## 2017.01.10

在`C/C++`中，可以有以下的用法：
```
int x = 0;
if (x) {
    // Do something.
}
```
但是，在Java里，Integer和Boolean是不兼容的，不可以有以上的用法。


* 在Java程序中，如果在同一个folder下，不用`import`
* 在Java的面向对象概念中并没有全局变量这回事。然而实际上会有需要方法或常量可以被任何的程序存取。`public`与`static`方法会让方法变成类似`global`的修饰符。在任何类中的任何程序都可以存取`public static`的方法。任何变量只要加上`public`, `static`, `final`，基本上都会变成全局变量取用的常数。
* 如果有大量的类的话，那就需要大量的文件，是否可以包装成类似单一应用程序的形式呢？可以把所有文件包装成一句pkzip格式来存档的java Archive .jar文件。在jar文件中可以引入一个简单的文字格式的文字文件，它被称为manifest，里面有定义出jar中的哪个文件带有启动应用程序的main方法。

### Primitive主数据类型和引用
类型 | 位数 | 值域
-- | -- | --
bollean | java虚拟机决定 | true或false
char | 16 bits | 0~65535
byte | 8 bits | -128~127
short | 16 bits | -32768~32767
int | 32 bits | -2147483648~2147483647
long | 64 bits | 很大
float | 32 bits | 规模可变
double | 64 bits | 规模可变

* 注意：`float f = 32.5f`，除非加上`f`，否则所有的小数点都被Java当做double处理。

### 创建对象数组
1. 声明一个Dog数组变量：`Dog[] pets`
2. 创建大小为7的Dog数组，并赋值给签名声明的`Dog[]`类型变量：`pets = new Dog[7]`
3. 创建新的Dog对象并将它们赋值给数组的元素。记得Dog数组中只带有Dog的引用变量。我们还是需要Dog对象：`pets[0] = new Dog();`

这其实有点诡异，与`C/C++`不太一样，`C/C++`在数组`new`的时候就会直接创建对象，但是看来`Java`并不是。

### 变量类型
* 变量有两种，primitive主数据类型和引用变量（存储对象的引用）
* 变量的声明必须有类型和名称
* primitive主数据类型变量值是该值的字节所表示的
* 引用变量的值代表位于堆值对象的存取方法（可理解为指针）
* 没有引用到任何对象的引用变量的值为`null`
* 数组一定是个对象，不管所声明的元素是否为primitive主数据类型。
