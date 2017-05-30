# Chapter7 复用类
复用类的方法有两种：
1. 只需要在新的勒种产生现有的对象。这种方式称为组合。
2. 继承现有的类，并添加新的代码，不改变原有类。这种方式称为继承

## 7.1 组合语法
编译器并不是简单的位每个引用都创建默认对象。如果要初始化类中的这些引用，可以用以下方法：
1. 在定义对象的地方。这意味着它们总能在构造器被调用前被初始化
2. 在类的构造器中
3. 在使用这些对象之前，这种方式被称为惰性初始化。在生成对象不值得及不逼每次都声称对象的情况下，这种方式可以减少额外的负担。

## 7.2 继承语法
在创建一个类时，总是在继承，因此除非明确指出要从其他类中继承，否则就是在隐式的从根类Object继承。
### 7.2。1 初始化基类
当创建了一个子类的对象时，该对象包含了一个基类的字子对象。这个子对象与你使用基类直接创建时的对象时一样的。二者的区别在于后者来自外部，而基类的子对象包装在子类对象的内部。

对基类子对象的正确初始化是非常重要的，而且也仅有在构造器中调用基类构造器来执行初始化这一种方式。如果没有特殊指定，Java会自动在子类的构造器中直接插入对基类构造器的调用。

构建过程是从基类开始的，所以基类在子类构造器可以访问它之前就已经完成了初始化。

如果基类没有默认的构造器，即构造器必须带参数或者想要调用一个带参数的基类构造器，就必须使用super显示的编写调用基类的构造器的语句。

## 7.3 代理
第三种关系成为代理，Java并没有提供对它的直接支持。这是继承与组合之间的中庸之道。这种方式是：我们将一个成员对象置于所要构造的类中，但是与此同时我们在新类中暴露了该成员对象所有的方法（就像继承）。

给一个使用代理的例子：
```
public class SpaceShipControls {
  void up(int velocity) {}
  void down(int velocity) {}
}

public class SpaceShipDelegation {
  private String name;
  private SpaceShipControls controls = new SpaceShipControls();
  public SpaceShipDelegation(String name) {
    this.name = name;
  }
  // Delegated methods
  public void up(int velocity) {
    controls.up(velocity);
  }
  public void down(int velocity) {
    controls.down(velocity);
  }
}
```
从上面的例子可以看到方法是如何传递给了底层的controls对象，二期接口由此也就与继承得到的接口相同了。但是使用代理时可以拥有更多的控制力，因为我们可以选择只提供在成员对象中的方法的某个子集。

Java语言不直接支持代理，但是很多开发工具却支持代理。例如使用JetBrains Idea IDE就可以制动生成上面的例子。

## 7.4 结合使用组合的继承
虽然编译器强制去初始化基类，并且要求在构造器的起始处就这么做，但是它并不监督必须将成员对象也初始化，所以自己必须注意这一点。

### 7.4.1 确保正确清理

## 7.7 向上转型
将子类的一个对象赋给一个基类的指针被称为向上转型。

### 7.7.1 为什么称为向上转型
由于向上转型是从一个较专用类型向较通用类型转换，所以总是安全的。所以说，子类是基类的一个超级，它可能比基类含有更多的方法，但是它必须至少具备积累中所有的方法。

### 7.7.2 再论组合与继承
要慎用继承，多使用组合。一个最清晰的判断方法就是问一问自己是否需要从子类向基类进行向上转型，如果必须向上转型，则继承是必要的，如果不需要，则应该好好考虑是否需要继承。

## 7.8 final关键字
### 7.8.1 final数据
有时数据的恒定不变是很有用的，比如：
1. 一个永不改变的编译时常量
2. 一个在运行时被初始化的值，而你不希望它被改变
对于编译期常量，编译器可以将该常量值带入任何可能用到它的计算式中，也就是说，可以再编译时执行计算式，这减轻了一些运行时的负担。在Java中，这类常量必须是基本数据类型，并且以关键字final表示。在对这个常量进行定义的时候，必须对其进行复制。

一个既是static又是final的域只占据一段不能改变的存储空间。

对于对象引用，final使引用很定不变。一旦引用被初始化指向一个对象，就无法再把它指向另一个对象。然而该对象自身是可以被修改的，Java并未提供使任何对象恒定不变的途径。这一限制同样适用数组，它也是对象。

Java允许“空白final”，所谓空白final是指被声明为final但是又未给定初值的域。无论什么情况，编译器都确保空白final在使用前必须初始化。但是空白final在关键字final的使用上提供了更大的灵活性，为此一个类中的final域就可以做到根据对象而有所不同。如果是空白final，必须在构造器中对齐进行初始化。

final参数。Java允许在参数列表中以生命的方式将参数指明为final。这意味着在方法中无法更改参数引用所指向的对象。

### 7.8.2 final方法
使用final方法的原因是把方法锁定，以防止任何继承类修改它的含义。这是要确保在继承中使用此方法保持不变，并且不会被覆盖。

以前曾有过另一个原因，不过不重要了。

类中所有的private方法都隐式的指定为final的。由于无法取用private方法，所以无法覆盖它。

### 7.8.3 final类
当某各类整体定义为final时，就表明不打算继承该类，而且也不允许别人这样做。如：
```
final class Dinosaur {
  int i = 7;
  int j = 1;
  ...
}
```
由于final类禁止继承，所以final类中所有的方法都隐式指定为时final的。在final类中可以给方法添加final修饰词，但这并不会添加任何意义。

## 7.9 初始化及类的加载
Java采用了一种不同的加载方式。每个类的变异代码都存在于它自己的独立的文件中。该文件只需要使用程序时才被加载。一般来说，类的代码在初次使用时才加载。这通常是指加载发生于创建类的第一个对象之时，但是当访问static域或者static方法时也会发生加载。因此Java不会出现C++的static交互引用初始化问题。初次使用之处页式static初始化发生之处，所有的static对象和static代码段都会在加载时依程序中的顺序而依次初始化。当然定义为static的东西只会被初始化依次。

### 7.9.1 继承与初始化
这里有必要使用一个例子来说明
```
class Insect {
  private int i = 9;
  protected int j;
  Insect() {
    print("i=" + i + ", j=" + j);
    j = 39;
  }
  private static int x1 = printInit("static Insect.x1 initialized");
  static int printInit(String s) {
    print(s);
    return 47;
  }
}

public class Beetle extends Insect {
  private int k = printInit("Beetle.k initialized");
  public Beetle() {
    print ("k=" + k);
    print ("j=" + j);
  }
  private static int x2 = printInit("static Beetle.x2 initialized");
  public static void main(String[] args) {
    print("Beetle constructor");
    Beetle b = new Beetle();
  }
}
/*Output:
static Insect.x1 initialized
static Beetle.x2 initialized
Beetle constructor
i = 9, j = 0
Beetle.k initialized
k = 47
j = 39
*/
```
Beetle上运行Jva时，第一件事就是试图访问Beetle.main()，于是加载器开始启动并找出Beetle类的编译代码。在对它进行加载的过程中，编译器注意到它有一个基类，于是它继续加载基类。不管是否打算产生一个基类的对象，这都要发生。如果该基类还有其自身的基类，那么第二个基类就会被加载。家下来，根基类的static初始化即被执行，然后是下一个子类。至此为止，必要的类都已经加载完毕，对象就可以被创建了。首先对象中所有的基本类型都会被设为默认值，对象引用被设为null。然后基类的构造器被调用。基类构造完成后，实例变量按其声明顺序进行初始化。最后构造器其余的部分被执行。
