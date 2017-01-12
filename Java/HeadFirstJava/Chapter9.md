## 构造器与垃圾收集器
当Java虚拟机启动时，它会从底层的操作系统中取得一块内存，并以此段来执行Java程序。所有对象都存活在可垃圾回收的堆上，而变量存在于哪一个空间（堆、栈）要看它是哪种变量。这里说的“哪种”不是它的类型，而是实例变量或局部变量。后者这种区域变量又被称为栈变量。
* 实例变量。实例变量是被声明在类里而不是方法里面。它们代表每个独立对象的“字段”。实例变量存在于所属的对象中。
* 局部变量。局部变量和方法的参数都是被声明在方法里。它们是暂时的，且声明周期只限于方法被放在栈上的这段期间。

### 要点
* 要记得非primitive的变量只是保存对象的引用而已，而不是对象本身。对象保存在堆里。不论对象是否声明或创建，如果局部变量是个对该对象的引用，只有变量本身会放在栈上。
* 对象引用变量与primitive主数据类型变量都是放在栈上。

### 实例变量
实例变量存在于对象所属的堆空间中。如果实例变量全都是primitive主数据类型的，则Java会依据primitive主数据类型的大小为该实例变量留下空间。int需要32位，long需要64位。但是如果实例变量是个对象的引用变量呢？

当一个新建对象带有对象引用的变量时，此时真正的问题是：是否需要保留对象带有的所有对象的空间。当然不是了。新建对象只会保留引用变量的空间。而此引用变量引用的对象所需要的空间依据该对象创建时间在另外在堆的另一块分配空间。

### 对象状态初始化
* Java可以有与类同名的方法而不会变成构造函数，其中的差别在于是否有返回类型。
* 构造函数不会被继承
* 编译器只会在完全没有自己写构造函数时才会帮助写默认的构造函数。
* 抽象类也有构造函数。
* 在执行构造函数时，一定会先执行父类的构造函数：`super()`，如果这个语句自己没有写，那编译器会帮忙加上，并且一定会调用没有参数的版本。**所以一定要有没有参数版本的构造函数**。如果要使用由参数版本的父类构造函数，则调用手动调用`super(parameter)`。
* 如果想在构造函数中调用另一个版本的构造函数，只需要使用`this`即可，如`this()`, `this(parameter)`。但注意，`this()`只能用在构造函数中，且必须是第一行语句，并且`super()`和`this()`不能兼得。

### 对象的生命周期
* 对象的声明周期要看对象的引用是否还活着。
* 局部变量只会存活在声明该变量的方法中。
* 实例变量的寿命与对象相同。如果对象还活着，则实例变量也会活着。
* Life与Scope的差别：
  * Life：只要有变量的堆栈块还存在于堆栈上，局部变量就还活着。也就是说，活到方法执行完毕为止。
  * Scope：局部变量的范围只限于声明它的方法之内。当次方法调用别的方法时，该变量还活着，但是不在范围内。
* 有三种方法可以释放对象的引用：
  * 引用永久性离开它的范围：`void go { Life z = new Life();}`
  * 引用被赋值到其他对象上：`Life z = new Life(); z = new Life();`
  * 直接将引用设定为`null`: `Life z = new Life(); z = null;`