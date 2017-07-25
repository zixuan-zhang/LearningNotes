# Chapter10 内部类

可以将一个类的定义放在另一个类的内部，这就是内部类。内部类是一种非常有用的特性，因为它允许你把逻辑相关的类组织在一起，并控制位于内部的类的可视性。在最初，内部类看起来就像是一种代码隐藏机制：将类置于其他类内部。但是内部类远不止如此，它了解外围类，并能与之通信，而且你用内部类写出的代码更加优雅而清晰。

## 10.2 链接到外部类
内部类的一个用途。当生成一个内部类对象时，此对象与制造它的外围对象之间就有了一种联系，所以它能访问其外围对象的所有成员，而不需要任何特殊条件。此外，内部类还拥有其外为类的所有元素的访问权。如下面的例子
```
interface Selector {
  boolean end();
  Object current();
  void next();
}

public class Sequence {
  private Object[] items;
  private int next = 0;
  public Sequence(int size) {
    items = new Object[size];
  }
  public void add(Object x) {
    if (next < items.length) {
      items[next++] = x;
    }
  }
  private class SequenceSelector implements Selector {
    private int i = 0;
    public boolean end() {
      return i == items.length;
    }
    public Object current() {
      return items[i];
    }
    public void next() {
      if (i < items.length) {
        i++；
      }
    }
  }
  public Selector selector() {
    return new SequenceSelector();
  }

  public static void main(String[] args) {
    Sequence sequence = new Sequence(10);
    for (int i = 0; i < 10; ++i) {
      sequence.add(Integer.toString(i));
    }
    Selector selector = sequence.selector();
    while (!selector.end()) {
      System.out.print(selector.current() + " ");
      selector.next();
    }
  }
}
```
如上面的例子，内部类自动拥有其对外为类所有成员的访问权。这是如何做到的呢？当某个外围类的对象创建了一个内部类对象时，此内部类对象必定会秘密地捕获一个指向那个外为类对象的引用。然后，在你访问此外为类的成员时，就是用哪个引用来选择外为类的成员。编译器会处理所有的细节，所以现在能看到：内部类的对象只能在与外围类的对象相关联的情况下才能被创建（即内部类是非static类时）。构建内部类对象时，需要一个指向其外为类的对象的引用，如果编译器访问不到这个引用就会报错。

## 10.3 使用.this和.new
**这是一个之前没有见过的用法，因此也不常见**

如果你需要生成对外部类对象的引用，可以使用外部类的名字后面紧跟圆点和this。这样产生的引用自动地具有正确的类型，这一点在编译器就被知晓并受到检查，因此没有任何运行时的开销。下面的例子展示了如何使用.this。这个用法非常奇怪，而且很绕。
```
public class DotThis {
  void f() {System.out.print("DotThis.f()");}
  public class Inner{
    public DotThis outer() {
      return DotThis.this;
    }
    public Inner inner() {
      return new Inner();
    }
    public static void main(String[] args) {
      DotThis dt = new DotThis();
      DotThis.Inner dti = dt.inner();
      dti.outer().f();
    }
  }
}
```
有时你可能想要告知其他对象去创建某个内部类的对象，要实现此目的，你必须在new表达式中提供对其他外部类对象的引用，这是需要使用.new的用法，就像这样：
```
public class DotNew {
  public class Inner{}
  public static void main(String[] args) {
    dn = new DotNew();
    DotNew.Inner dni = dn.new Inner();
}
```
这个的解释的关键在于在static函数里，并没有该类的对象，因此必须先创建该类的对象，用使用该类的对象来创建该对象的内部类。这个用法也挺奇怪的。

## 10.4 内部类与向上转型
## 10.5 在方法和作用域内的内部类
可以再一个方法里面或者任意的作用域内定义内部类。有两个理由：
1. 实现了某类型的接口，于是可以创建并返回对其的引用
2. 要解决一个复杂的问题，创建一个类来辅助你的解决方案，但又不希望这个类是公共可用的。

内部类可以是：
1. 一个定义在方法中的类
2. 一个定义在作用域内的类，此作用域在方法的内部
3. 一个实现了接口的匿名类
4. 一个匿名类，它扩展了有非默认构造器的类
5. 一个匿名类，它执行字段初始化
6. 一个匿名类，它通过实例初始化实现构造（匿名类不可能有构造器）

有关的例子去看书吧。

## 10.6 匿名内部类
现在看一个奇怪的例子。
```
public class Parcel7 {
  public Contents contents() {
    return new Contents() {
      private int i = 11;
      public int value { return i; }
    }
  }
  public static void main(String[] args) {
    Parcel7 p = new Parcel7();
    Contents c = p.contents();
  }
}
```
contents()方法将返回值的生成与表示这个返回值的类的定义结合在一起！另外，这个类是匿名的，Contents并不是这个类的名字，而是这个类的 **基类** ！！！。看起来你似乎是要创建一个Contents对象，但是然后你却说：我想在这里插入一个类的定义。这种奇怪的语法指的是：创建一个继承自Contents匿名类对象。通过new表达式返回的引用被自动向上转型为Contents的引用。

在这个匿名类内部，使用了默认的构造器来生成Contents，下面的代码展示的是，如果你的基类需要有一个参数的构造器，那么应该：
```
public class Parcel8 {
  public Warpping warpping(int x) {
    // Base constructor call
    return new Wrapping(x) { // Pass constructor argument
      public int value() {
        return super.value * 47;
      }
    };
  }

  public static void main(String[] args) {
    Parcel8 p = new Parcel8();
    Warpping w = p.warpping(10);
  }
}
```
只需要简单的传递合适的参数给基类的构造器即可，这里讲x传给new Warpping(x)。尽管Warpping只是一个具有具体实现的普通类，但它还是被子类当做公共接口来使用。

在匿名类中定义字段时，还能够对齐执行初始化操作：
```
public class Parcel9 {
  public Destination destination(final String dest) {
    return new Destination() {
      private String label = dest;
      public String readLabel() {
        return label;
      }
    }
  }
}
```
需要注意的是，如果定义一个匿名内部类，并且希望它使用一个在其外部定义的对象，那么编译器会要求其参数引用是final的，就像上面那个例子一样。如果不这样，则会得到一个编译时错误消息。但是，当如果这个对象时赋给匿名类的基类的构造器，不是显示的使用时，可以不使用final。

但是，如果想要做一些类似构造器的行为，那么该怎么办呢？在匿名类中不可能有明明的构造器，但是通过实例初始化，就能达到匿名内部类创建一个构造器的效果，就像这样：
```
public class Parcel10 {
  public Destination destination(final String dest, final float price) {
    private int cost;
    {
      cost = Math.round(price);
      if (cost > 100) {
        System.out.print("...")
      }
    }
    private String label = dest;
    public String readLabel() {
      return label;
    }
  }
}
```

匿名内部类与正规的继承相比有些受限，因为匿名内部类既可以扩展类，也可以实现接口，但是不能两者兼备。如果是实现接口，也只能实现一个接口。

## 10.7 嵌套类
如果不需要内部类对象与其外围对象之间有联系，那么可以将内部类声明为static。这通常称为嵌套类。普通的内部类对象隐式的保存了一个引用，指向创建它的外为类对象。当内部类是static的时候，就不是这样，意味着：
1. 要创建嵌套类对象，并不需要其外为类的对象
2. 不能从嵌套类的对象中访问非静态的外为类对象。

嵌套类与普通的内部类还有一个区别。普通内部类的字段与方法，只能放在类的外部层次上，所以普通的内部类不能有static数据和static字段，也不能包含嵌套类。但是嵌套类可以包含这些东西。

### 10.7.1 接口内部的类
正常情况下，不能再接口内部放置任何代码，但嵌套类可以作为接口的一部分。你放到接口中的任何类都自动地是public和static的。因为类是static的，只是将嵌套类置于接口的命名空间内，这并不违反接口的规则。你甚至可以在内部类中实现其外围接口，就像这样：
```
public interface ClassInInterface {
  void howdy();
  class Test implements ClassInInterface {
    public void howdy() {
      System.out.print("Howdy!");
    }
  }
}
```

## 10.8 为什么需要内部类
一般来说，内部类继承自某个类或者实现了该接口，内部类的代码操作创建它的外为类的对象。所以可以认为内部类提供了某种进入外围类的窗口。但是，如果只是需要一个对接口的引用，为什么不通过外围类实现那个接口呢？答案是：如果这能满足要求，就应该这样做。那么内部类实现一个接口与外围类实现这个接口有什么区别？答案是：后者并不总能享用到接口带来的方便，有时需要用到接口的实现。所以，内部类最吸引人的原因是：**每个内部类都能独立地继承自一个（接口的）实现，所以无论外围类是否已经继承了某个（接口的）实现，对于内部类都没有影响**。

这个理解起来有点困难，书上说的是，内部类在多重继承类时很有用。具体的太多了，自己看书吧。

如果不需要解决“多重继承”的问题，就不需要使用内部类。但是如果使用内部类，就可以获得其他特性：
1. 内部类可以有多个实例，每个实例都有自己的状态信息，并且与其外围类对象的信息相互独立
2. 在单个外围类中，可以让多个内部类以不同的方式实现同一个接口，或继承统一各类。
3. 创建内部类对象的时刻并不依赖于外围类对象的创建。
4. 内部类并没有令人迷惑的”is-a“关系，它就像是独立的实体。

**到了这发现内部类还是很难理解的，尤其是关于内部类解决多重继承问题，还有闭包的思想，需要多看几遍，多实践多思考**
