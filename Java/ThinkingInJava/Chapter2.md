# 第二章 一切皆是对象
## 2.2 必须由你创建所有对象
### 2.2.1 Java存储地方
1. 寄存器
2. 栈。基本类型和引用存储在栈中。
3. 堆。
4. 常量存储。常量值通常直接存放在程序代码内部。
5. 非RAM存储。比如流对象和持久化对象。
### 2.2.2 基本类型
* 基本类型和包装器类型可以双向转换。如
```
char c = 'x';
Character ch = new Character(c);
Character ch = new Character('x');
Character ch = 'x';
char c = ch;
```
## 2.5 方法、参数和返回值
* Java中用 **方法** 来表示 **函数** 的意义。

## 2.6 构建一个Java程序
### 2.6.3 static关键字
* 静态方法存储于Permanent Generation area，我目前理解为静态区。这个静态区存储在Heap里。包括函数的方法应该也是存储在这个地方。
