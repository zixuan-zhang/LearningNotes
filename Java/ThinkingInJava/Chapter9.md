# Chapter9 接口
## 9.1 抽象类和抽象方法
Java提供一种抽象方法机制，这种方法是不完整的，仅有声明没有方法体。语法是：`abstract void f()`包含抽象方法的类叫做抽象类。如果一个雷包含一个或多个抽象方法，该类必须被限定为抽象的。

如果从一个抽象类继承，并想创建该类的对象，那么久必须为基类的所有抽象方法提供方法定义。如果不这样做（可以不做这样），子类就必须也是抽象的，而且编译器将会强制用abstract关键字来限定这个类。

## 9.2 接口
abstract关键字允许人么在类中创建一个或多个没有任何定义的方法——提供了接口部分，但是没有提供任何相应的具体实现，这些实现是由此类的继承者创建的。interface这个关键字产生一个完全抽象的类，它根本没有提供任何具体的实现。它允许创建者确定方法名、参数列表和返回类型，但是没有任何方法体。接口只提供了形式，而未提供任何具体实现。接口用来建立类与类之间的协议。

但是interface不仅是一个极度抽象的类，因为它允许人们通过创建一个能够向上转型为多种基类的类型，来实现某种类似多重继承变种的特性。

要想穿件一个接口，需要用interface关键字来替代class关键字。可以再interface关键字前加public关键字（但仅限于该接口在与其同名的文件中被定义）。如果不添加public关键字，则它只具有包访问权限，这样它就只能在同一个包内可用。接口也可以包含域，但是这些域隐式的是static和final的。

要让一个类遵循某个特定的接口或者一组接口，需要使用implements关键字，表明我要来实现它是如何工作的。

可以选择在接口中显示的将方法声明为public的，但是即使不这样做，也是public的。因此，当要实现一个接口时，**在接口的定义的方法必须被定义为是public的**，否则将只能得到默认的包访问权限，这样在方法被继承的过程中，其访问权限就被降低了，Java编译器不允许这样做。

## 9.4 Java中的多重继承
## 9.5 通过继承来扩展接口
通过继承，可以很容易地在接口中添加新的方法声明，还可以通过继承在新接口中组合数个接口。如下面的例子：
```
interface Monster {
  void menace();
}
interface DangerousMonster extends Monster {
  void destroy();
}
interface Lethal {
  void kill();
}
class DragonZilla implements DangerousMonster {
  public void menace() {}
  public void destroy() {}
}
interface Vampire extends DangerousMonster, Lethal {
  void drinkBlood();
}
class VeryBadVampire implements Vampire {
  public void menace() {}
  public void destroy() {}
  public void kill() {}
  public void drinkBlood() {}
}
public class HorroShow {
  static void u(Monster b) {b.menace();}
  static void v(DangerousMonster d) {
    d.menace();
    d.destroy();
  }
  static void w(Lethal l) {l.kill();}
  public static void main(String[] args) {
    DangerousMonster barney = new DragonZilla();
    u(barney);
    v(barney);
    Vampire vlad = new VeryBadVampire();
    v(vlad);
    u(vlad);
    w(vlad);
  }
}
```

### 9.5.1 组合接口时的名字冲突
在实现多重继承时可能会碰到一个陷阱。如果再继承或实现接口的时候出现函数是完全相同（包括参数和返回值类型都相同）没有问题；如果函数名相同，参数不同，被认为是重载；如果函数名相同，参数相同，只是返回值不同，则会报错。在设计的时候要避免这种情况。

## 9.6 适配接口
## 9.7 接口中的域
因为你放入接口中的任何域都自动是static和final的，所以接口就称为了一种很便捷的用来创建常量组的工具。在Java SE5前会有这种用法，但是之后使用enum来代替了。如下面的：
```
public interface Months {
  int JANUARY = 1, FEBRUARY = 2, ...;
}
```

### 9.7.1 初始化接口中的域
在接口中定义的域不能是“空final”，但是可以被非常量表达式初始化。例如：
```
public interface RandVals {
  Random RAND = new Random(47);
  int RANDOM_INT = RAND.nextInt(10);
}
```
既然是static的，它们就可以再类第一次被加载时初始化，这发生在任何域首次被访问时。这些域不是接口的一部分，它们的值被存储在该接口的静态存储区域内。

## 9.9 接口与工厂
接口时实现多重继承的途径，而生成遵循某个接口的对象的典型方式就是工厂方法设计模式。这与直接调用构造器不同，我们在工厂对象上调用的是创建方法，而该工厂对象将生成接口的某个实现的对象。通过这种方式，代码将完全与接口的实现分离，这就使得我们可以透明的将某个实现替换为另一个实现。
