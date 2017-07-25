# Chapter8 多态

## 8.2 转机
### 8.2.1 方法调用绑定
将一个方法调用同一个方法主体关联在一起被称作绑定。若在程序执行前进行绑定（如果有的话，由编译器和连接程序实现）叫做前期绑定。后期绑定是指在运行时根据对象的类型进行绑定，也被称为动态帮顶或者运行时绑定。也就是说必须有某种机制在运行时能判断对象的类型，从而调用恰当的方法。也就是说编译器一直不知道对象的类型，但是方法调用机制能够找到正确的方法体，并加以调用。后期绑定机制随编程语言的不同而有所不同，但是不管怎样都必须在对象只能怪安置某种类型信息。

Java中除了static方法和final方法(private属于final方法)，其他所有的方法都是后期绑定

### 8.2.2 产生正确的行为
### 8.2.4 缺陷：“覆盖”私有方法
这里要用个例子来说明
```
public class PrivateOverride {
  private void f() {print("private f()");}
  public static void main(String[] args) {
    PrivateOverride po = new Derived();
    po.f();
  }
}
class Derived extends PrivateOverride {
  public void f() {print("public f()");}
}

/* Output
private f()
*/
```
也许有的人的期望输出是public f(),但是由于private方法被自动认为是final方法，而且对子类是屏蔽的。因此，Derived类中的f()方法就是一个全新的方法；既然基类只能怪的f()方法在子类Derived中不可见，因此甚至不能被重载。

所以，只有非private方法才能被覆盖，但是还需要密切中医覆盖private方法的现象，这时虽然编译器不会报错，但是也不会按照所期望的来执行。所以说，在子类中，对于基类中的private方法，最好采用不同的名字。

### 8.2.5 缺陷：域与静态方法
所有的域都是静态绑定。而如果某个方法是静态的，它的行为就不具有多态性，因此也不会动态绑定，因此在调用时只会使用该类对应的静态方法。当然静态方法是可以被子类覆盖的。

## 8.3 构造器好多态
复杂对象的调用构造器的调用顺序（不包括静态函数成员）
1. 调用基类构造器
2. 按声明顺序调用成员的初始化方法
3. 调用子类的构造器主体

### 8.3.3 构造器内部的多态方法的行为
如果要在构造器内部调用一个动态绑定方法，就要用到那个方法的被覆盖后的定义。然而，这个结果的效果比较难预料，因为被覆盖的方法在对象呗完全构造之前就会被调用。因此，在编写构造器时的一个有效的准则是：用尽可能简单的方法使对象进入正常状态；如果可以的话，避免调用其他方法。在构造器内唯一能够安全调用的那些方法使基类只能怪的final方法。这些方法不能被覆盖，因此不会出现上诉问题。（如果无法理解，就自己看书看例子）

## 8.4 协变返回类型
Java SE5中添加了协变返回类型，它表示在导出类中的被覆盖方法可以返回基类方法的返回类型的某种子类。Java SE5与Java早期版本的差异就在于覆盖版本不能返回子类。