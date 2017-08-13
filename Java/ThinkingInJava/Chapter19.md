# 第19章 枚举类型

关键字enum可以将一组具名的值的有限集合创建为一种新的类型，而这些具名的值可以作为常规的程序组件使用。

## 19.1 基本enum特性

调用enum的values()方法可以遍历enum实例。values()方法返回enum实例的数组，而且该数组中的元素严格保持其在enum声明时的顺序，因此可以在循环中使用values()返回的数组。

创建enum时，编译器会为你生成一个相关的类，这个类集成自java.lang.Enum.有些有趣的方法：

```java
enum Shrubbery {GROUP, CRAWLING, HANGING}
for (Shrubbery s : Shrbbery.values()) {
  s.ordinal(); // Print decralation order
  s.getDeclaringClass(); // Print Shrubbery
  s.name(); // Print enum name.
}
```

## 19.2 向enum中添加新方法

除了不能继承自一个enum之外，基本上可以将enum看作一个常规的类。也就是说，可以向enum中添加新方法。

一般来说，我们希望每个枚举实例能够返回队自身的描述，不仅仅是默认的toString。因此，可以提供一个构造器，专门负责处理这个额外的信息，然后再添加一个方法，返回这个描述信息。如：

```java
public enum OzWitch {
  WEST("This is west"),
  NORTH("This is north");
  private String desc;
  private OzWitch(String desc) {
    this.desc = desc;
  }
  public String getDesc() {return desc;}
  public static void main(String[] args) {
    for (OzWitch witch : OzWitch.values()) {
      print(witch + ":" + witch.getDescription());
    }
  }
}
```

**注意**：如果打算定义自己的方法，必须在enum实例序列的最后添加一个分号。同时，Java要求必须先定义enum实例。如果在定义enum实例之前定义了任何方法或属性，就会出现编译错误。

在这个例子中，虽然我们有意识的将enum的构造器声明为private，但是对于它的访问性而言，并没有什么变化，因此即使不声明private，我们也只能在enum定义的内部使用其构造器创建enum实例。一旦enum定义结束，编译器就不允许我们再使用其构造器来创建任何实例了。

### 19.2.1 覆盖enum的方法

可以覆盖enum父类的方法，比如`toString()`

## 19.3 switch语句中的enum

## 19.4 values()的神秘之处

values()方法不是父类Enum的函数，也不是enum类的函数，而是编译器自动添加的。

## 19.5 实现，而非继承

所有的enum都集成自java.lang.Enum类。由于Java不支持多重继承，所以enum不能再继承其他类。然而，我们在创建一个新的enum时，可以同时实现一个或多个接口

```java
enum CartoonCharacter implements Generator<CartoonCharacter> {
  SLAPPY, SPANKY, PUNCHY, SILLY, BOUNCY, NUTTY, BOB;
  private Random rand = new Random(47);
  public CartoonCharactor next() {
    return values()[random.nextInt(values().length)];
  }
}
public class EnumImplementation {
  public static<T> void printNext(Generator<T> rg) {
    System.out.println(rg.next() + ". ");
  }
  public static void main(String[] args) {
    ...
  }
}
```

## 19.7 使用接口组织枚举

无法从enum继承子类令人沮丧，这种需求源自我们希望扩展原enum中的元素，有时因为我们希望使用子类一个enum中的元素进行分组。

在一个接口的内部，创建实现该接口的美剧，以此将元素进行分组，可以达到将枚举元素分类组织的目的。举例来说，假设希望用enum来表示不同类别的食物，同时还希望每个enum元素仍然保持Food类型。可以这样实现

```java
public interface Food {
  enum Appetizer implements Food{
    SLAD, SOUP;
  }
  enum MainCourse implements Food{
    LASAGNE, BURRITO, PAD_THAI;
  }
}
```

对于enum而言，实现接口是使其子类化的唯一办法，所以嵌入在Food中的每个enum都实现了该Food接口。

```java
puclic class TypeOfFood {
  public static void main(String[] args) {
    Food food = Appetizer.SLAD;
    food = MainCorse.LASAGNE;
  }
}
```

如果enum类型实现了Food接口，那么我们就可以将其实例向上转型为Food，所以上例中所有东西都是Food。

## 19.8 使用EnumSet替代标志

EnumSet是为了通过enum创建一种替代品，以替代传统的机遇int的“位标志”。这种标志可以用来表示某种“开／关”信息，不过，使用这种标志，我们最终操作的只是一些bit，而不是这些bit想要表达的概念。

详细再看书吧，暂时没仔细看。

## 19.9 使用EnumMap（没看）

## 19.10 常量相关的方法

Java的enum有趣的特性，就是允许程序员为enum实例编写方法，从而为每个enum实例赋予各自不同的行为。要实现常量相关的方法，你需要为enum定义一个或多个abstract方法，然后为每个enum实例实现该抽象方法。

```java
public enum ConstantSpecificMethod {
  DATE_TIME {
    String getInfo() {
      return DateFormat.getDateInstance().format(new Date);
    }
  },
  CLASSPATH {
    String getInfo() {
      return System.getenv("CLASSPATH");
    }
  },
  VERSION {
    String getInfo() {
      return System.getProperty("java.version");
    }
  };
  abstract String getInfo();
  public static void main(String[] args) {
    for (ConstantSpecificMethod csm : values()) {
      System.out.println(csm.getInfo());
    }
  }
}
```

通过相应的enum实例，我们可以调用其上的方法，这通常也称为表驱动的代码。

在面向对象的程序设计中，不同的行为与不同的类关联。而通常常量相关的方法，每个enum实例可以具备自己独特的行为，这似乎说明每个enum实例就像一个独特的类。在上面的例子中，enum实例似乎被当作其”超类“ConstanceSpecificMethod来使用，在调用getInfo()方法时，体现多态的行为。然而，enum实例与类相似之处仅限于此，我们并不能真的将enum实例作为一个类型来使用。

