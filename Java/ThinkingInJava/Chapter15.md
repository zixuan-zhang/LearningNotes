# 第15章 泛型

## 15.2 简单泛型

类型参数：尖括号括住，放在类名后面。在使用这个类时，再用实际的类型替换此类型参数。

```java
public class Holder<T> {
  private T a;
  public Holder3(T a) {this.a = a;}
  public void set(T a) {this.a = a;}
  public T get() { return a;}
}
```

### 15.2.1 一个元组类库

仅一次方法调用就可以返回多个对象，这个需求比较常见，但是return语句只能返回单个对象，因此解决办法就是创建一个对象，用它来持有想要返回的多个对象。当然，可以在每次需要的时候，专门创建一个类来完成这样的工作。但是有了泛型，就能一次性解决该问题，以后再也不用浪费时间。同时，我们在编译期就能确保该类型安全。

这个概念称为元组（tuple），它是将一组对象直接打包存储于其中的一个单一对象。这个容易对象允许读取其中的元素，但是 不允许存放新对象。

通常，元组可以具有任意长度，同时元组中的对象可以是任意不同的类型。不过，我们希望能够为每个对象指明其类型，并从容器中读取出来，得到正确的类型。遗憾的是，我们不能简单的指明长度，只能对不同长度的需求创造不同的类。

```java
public class TwoTuple<A, B> {
  public final A first; // 注意是final，因此不能被赋值
  public final B second;
  public TwoTuple(A a, B b) {first = a; second = b;}
}
public class ThreeTuple<A, B, C> extends TwoTuple<A, B> {
  public final C third;
  public ThreeTuple(A a, B b, C c){
    super(a, b);
    third = c;
  }
}
```

## 15.3 泛型接口

泛型也可以用于接口，例如生成器。一般而言，生成器只定义一个方法，该方法用以产生新的对象。

类在实现该接口的时候可以将类具体化。

## 15.4 泛型方法

泛型不仅可以用于整个类上，也可以在类中包含参数化方法，而这个方法所在的类可以事泛型类，也可以不是泛型类。

泛型方法使得该方法独立于类儿产生变化。一个基本的指导原则：无论何时，只要你能做到，你就应该尽可能使用泛型方法。另外，对于一个static的方法而言，无法访问泛型类的类型参数，所以如果static方法需要使用泛型能力，就必须使其成为泛型方法。

要定义泛型方法，只需要将泛型参数列表置于返回值值钱，就像这样：

```java
public class GenericMethods {
    public <T> void f(T x) {
        System.out.println(x.getClass().getName());
    }
}
```

注意：当使用泛型类时，必须在创建对象的时候指定参数类型的值，而使用泛型方法的时候，通常不必指明参数类型，因为编译器会为我们找出具体的类型，这成为类型参数推断。

### 15.4.2 可变参数与泛型方法

泛型方法可以与可变参数列表很好的共存：

```java
public class GenericVarargs {
    public static<T> List<T> makeList(T... args) {
        List<T> result = new ArrayList<T>();
      	for (T item : args) {
            result.add(item);
        }
      	return result;
    }
}
```

### 15.4.3 用于Generator的泛型方法

### 15.4.6 一个set实用工具

作为泛型方法的另一个实例，看看如何Set来表示数学中的关系式。

```java
public class Sets {
    public static<T> Set<T> union(Set<T> a, Set<T> b) {
        Set<T> result = new HashSet<T>(a);
      	result.addAll(b);
      	return result;
    }
  	public static<T> Set<T> intersection(Set<T> a, Set<T> b) {
        Set<T> result = new HashSet<T>(a);
      	result.retainAll(b);
      	return result;
    }
}
```

## 15.5 匿名内部类

## 15.7 擦出的神秘之处

当你开始深入钻研时，会发现大量的东西最初看起来是没有意义的。尽管可以声明`ArrayList.class`，但是不能声明`ArrayList<Integer>.class`。考虑下面情况：

```java
public class ErasedTypeEquivalence {
    public static void main(String[] args) {
        Class c1 = new ArrayList<String>.getClass();
      	Class c2 = new ArrayList<Integer>.getClass();
      	// c1 == c2
    }
}
```

`ArrayList<String>`和`ArrayList<Integer>`很容易被认为是不同的类型。不同的类型在行为上肯定不同，但是上面的程序认为她们是相同的类型。

在泛型代码内部，无法获得任何有关泛型参数类型的信息。因此，你可以指导诸如类型参数标示符合泛型类型边界这类的信息，却无法知道用来创建某个特定实例的类型参数。Java泛型，是使用擦出来实现的，意味着党在使用泛型时，任何具体的类型信息都被擦除了，你唯一知道的就是在使用一个对象。因此`List<Integer>`和`List<String>`在运行时事实上时相同的类型。这两种都被擦除成她们家的“原生”类型，即List。

### 15.7.1 C++的方式

```c++
template<class T> class Manipulator {
  T obj;
public:
  Manipulator(T x) {obj = x;}
  void manipulate() {obj.f();}
};
class HasF{
public:
  void f() {}
};
int main() {
    HasF hf;
  Manipulator<HasF> manipulator(hf);
  manipulator.manipulate();
}
```

`Manipulator`类存储一个类型T的对象，它在manipulate()中调用了obj的方法f()。它怎么知道f()方法是为类型参数T而存在的呢？当实例化这个模板时，C++编译器将进行检查，因此在`Manipulator<HasF>`被实例化的这一刻，它看到HasF拥有一个方法`f()`。如果情况并非如此，就会得到一个编译器错误。

用C++编写这种代码很简单，因为当模板被实例化时，模板代码知道其模板参数的类型。Java泛型就不同了。

```java
public class HasF {
  public void f() {}
}
class Manipulator<T> {
  private T obj;
  public Manipulator(T x) {obj = x;}
  public void manipulate() {obj.f();}
}
```

由于有了擦除，Java编译器无法将`manipulate()`必须能够在obj上调用`f()`这一需求映射到HasF拥有`f()`这一事实上。为了调用`f()`，我们必须协助泛型类，给定泛型类的边界，如下：

```java
class Manipulate<T extends HasF> {
  private T obj;
  public Manipulate(T x) {obj = x;}
}
```

边界`<T extends HasF>`声明T必须具有类型HasF或者HasF导出的类型。

### 15.7.2 迁移兼容性

擦除时Java泛型实现中的一种折中。

在基于擦除的实现中，泛型类型被当作第二类类型处理，即不能在某些重要的上下文环境中使用的类型。泛型类型只有在静态类型检查期间才出现，在此之后，程序中的所有泛型类型都将被擦除，替换为他们的非泛型上街。诸如`List<T>`这样的类型注解将被擦除为`List`，而普通的类型变量在未指定边界的情况下将被擦除为`Object`。

擦除的核心动机时它使得泛化的客户端可以使用非泛化的类库来实现，反之亦然，这经常被称为“迁移兼容性”。在理想情况下，当所有的事物都可以同时被泛化时，它们就可以专注于此。在现实中，即使程序员只编写泛型代码，它们也必须处理在Java SE5之前编写的非泛型类库。

一句话来说，擦除是由于历史原因造成的。

### 15.7.3 擦除的问题

擦除主要的正当理由是从非泛化代码到泛化代码的转变过程，以及在不被破坏现有类库的情况下，将泛型融入Java语言。擦除使得现有的非泛型客户端代码能够在不改变的情况下继续使用。

擦除的代价是显著的。泛型不能用于显式的饮用运行时类型的操作之中，例如转型、instanceof和new表达式。因为所有关于参数的类型信息都丢失了。无论何时，你在编写代码时，必须时刻提醒自己，你只是看起来好像拥有有关参数的类型信息而已。

```java
class GenericBase<T> {
  private T element;
  public void set(T arg) { element = arg;}
  public T get() { return element; }
}
class Derived1<T> extends GenericBase<T> {}
class Derived2 extends GenericBase{} // No warning

// class Derived3 extends GenericBase<?> {}
// Strange error:
//   unexpected type found : ?
//   required: class or interface without bounds
public class ErasureAndInheritance {
  @SuppressWarnings("unchecked")
  public static void main(String[] args) {
    Derived2 d2 = new Derived2();
    Object obj = d2.get();
    d2.set(obj); // Warning here
  }
}
```

Derived2继承自GenericBase，但是没有任何泛型参数，而编译器不会发出任何警告。警告在`set()`被调用时才出现。

为了关闭警告，Java提供了一个注解，我们可以在列表中看到它：

`@SuppressWarnings("unchecked")`

注意，这个注解被放置在可以产生这类警告的方法之上，而不是整个类上。当你要关闭警告时，最好聚焦，不太太宽泛。

可以推断，Derived3产生的错误意味着编译器期望得到一个原生基类。

### 15.7.4 边界处的动作

## 擦除的补偿

正如我们看到的，擦除丢失了在泛型代码中执行某些操作的能力。例如：

```java
public class Erased<T> {
  private final int SIZE = 10;
  public static void f(Object arg) {
    if (arg instance of T) { // Error
      T var = new T();       // Error
      T[] array = new T[SIZE]; // Error
      T[] array = (T)new Object[SIZE]; // Unchecked warning
    }
  }
}
```

偶尔可以绕过这些问题来编程，但是有时必须通过引入类型标签来对擦除进行补偿。这意味着你需要显示地传递你的类型的Class对象，以便可以在类型表达式中使用它。

例如，在前面例子中对使用instanceof的尝试失败了，因为其类型信息已经被擦除了。如果引入类型标签，就可以转而使用动态的isInstance():

```java
class Building {}
class House extends Building {}
public class ClassTypeCpature<T> {
    Class<T> kind;
  	public ClassTypeCapture(Class<T> kind) {
        this.kind = kind;
    }
  	public boolean f(Object arg) {
        return kind.isInstance(arg);
    }
  	public static void main(String[] args) {
        ClassTypeCapture<Building> c = new ClassTypeCapture<Building>(Building.class);
    }
}
```

### 15.8.1 创建类型实例

创建一个`new T()`的尝试将无法实现，部分原因是因为擦除，而另一部分原因是因为编译器不能验证T具有默认无参数构造器。C++中，这种操作很自然，并且很安全，因为它是在编译期受到检查的。

```c++
template<class T> Class Foo{
    T x;
  	T* y;
public:
  	Foo() {y = new T();}
}
```



Java中的解决方案是传递一个工厂对象，并使用它来创建新的实例。最便利的工厂对象就是Class对象，因此如果使用类型标签，那么久可以使用newInstance()来创建这个新对象。

```java
class ClassAsFactor<T> {
  T x;
  public ClassAsFactory(Class<T> kind) {
    try {
        x = kind.newInstance();
    } catch (Exception e) {
        
    }
  }
}
class Employee{}
public class InstanceGenericType {
  public static void main(String[] args) {
    ClassAsFactory<Employee> fe = new ClassAsFactory<Employee>(Employee.class);
    try {
        ClassAsFactory<Integer> fi = new ClassAsFactory<Integer>(Integer.class);
    } catch (Exception e) {
        
    }
  }
}
```

这个可以编译，但是会因为`ClassAsFactory<Integer>`而失败，因为Integer没有任何默认的构造器。因为这个错误不是在编译期补货的。有的建议使用显示的工厂，并将限制其类型，使得职能接受实现了这个工厂的类：

```java
interface Factory<T> {
  T create();
}
class Foo2<T> {
  private T x;
  public <F extends Factory<T>> Foo2(F factory) {
    x = factory.create();
  }
}
class IntegerFactory implements Factory<Integer> {
  public Integer create() {
      return new Integer(0);
  }
}
class Widget {
  public static class Factory implements Factory<Widget> {
    public Widget create() {
      return new Widget;
    }
  }
}
```

### 15.8.2 泛型数组

## 15.9 边界

边界使得可以在用于泛型的参数类型上设置限制条件。尽管使得可以强制规定泛型可以应用的类型，但是其潜在的一个更重要的效果是你可以按照自己的边界类型来调用方法。

因为擦除移除了类型信息，所以可以用无界泛型参数调用的方法只是哪些可以用Object调用的方法。如果能够将这个参数限制为某个类型子集，那么你就可以用这些类型子集来调用方法。但是如果能够将这个参数限制为某个类型子集，那么你就可以用这些类型子集来调用方法。为了执行这种限制，Java泛型重用了extends关键字。

## 15.10 通配符

有时你想要在两个类型之间建立某种类型的向上转型关系，这正是通配符允许的：

```java
public class GenericAndCovariance {
    public static void main(String[] args) {
        List<? extends Fruit> fruitList = new ArrayList<Apple>();
      	
    }
}
```

