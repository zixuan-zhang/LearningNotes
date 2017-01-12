## 接口与抽象类

抽象类是不希望被实例化的类，只需要在类的声明前面接上关键词`abstract`就好。
```
abstract class Animal {
    public void roam() {}
}
```

### 抽象的方法
* 除了类之外，你也可以将方法标记为`abstract`的。抽象的类代表此类必须要被extend过，抽象的方法代表此方法一定要被覆盖过。
* 抽象的方法没有实体。`public abstract void eat();`
* 如果声明了一个抽象的方法，就必须将类也标记为抽象的。你不能在非抽象类中拥有抽象方法。
* 抽象的方法没有实体，它只是为了标记处多态而存在。这表示在继承树结构下的第一个具体类必须实现出所有的抽象方法。
* 抽象类可以带有抽象的和非抽象的方法。

### Object类
* 在java中的所有类都是从`Object`这个类继承过来的。`Object`类是所有类的父类。
* `Object`类不是抽象的。因为它可以被所有类继承下来的方法都实现程序代码，所以没有必须被覆盖过的方法。
* `Object`类有两个主要的目的：作为多态让方法可以应付多种类型的机制，以及提供Java在执行期对任何对象都有需要的方法实现程序代码。有一部分的方法与线程有关。
* 既然多态类型这么有用，为什么不把所有的参数和返回值都设定为`Object`类型呢？“类型安全检查”是Java保护程序代码的一项重要的机制。在此机制下，你不会意外地要求对象执行错误类型的动作。但事实上，你也不用担心会发生这件事，因为当某个对象时以`Object`类型来引用时，Java会把它当做`Object`类型的实例。这意味着你只能调用`Object`类型所声明的方法。

### 转换回原来的类型
```
Object o = new Dog();
Dog d = (Dog) o;
d.roam();
```
如果不确定是否是Dog类型，可以使用`instanceof`这个运算符来检查。若是类型转换错了，你会执行期遇到ClassCastException异常并终止。
```
if (o instanceof Dog) {
    Dog d = (Dog) o;
}
```

### 接口
接口可以解决多重继承问题。Java的接口就好像是100%的纯抽象类。所有接口的方法都是抽象的。
* 接口的定义：`public interface Pet {}`
* 接口的实现：`public class Dog implements Pet {}` 。使用`implements`这个关键字。

```
public interface Pet {
    public abstract void beFriendly();
    public abstract void play();
}

public class Dog extends Canine implements Pet {
    public void beFriendly() {...} // 必须在这里实现Pet的方法
    public void play() 
    public void roam() {...}
    public void eat() {...}
}
```

* 使用接口就可以继承超过一个以上的来源。

### 要点
* 如果不想某各类被初始化，就以`abstract`这个关键词将它标记为抽象的
* 抽象类可以带有抽象的和非抽象的方法
* 如果累带有抽象的方法，则此类必定标识为抽象的
* 抽象的方法没有定义，它的声明是以分号结束的
* 抽象的方法必须在具体的类中运行
* Java不允许多重继承
* 实现某接口的类必须实现它所有的方法，因为这些方法都是`public`和`abstract`的
*
