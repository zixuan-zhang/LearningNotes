# 第16章 数组

## 16.1 数组为什么特殊

数组与其他种类的容器之间的区别有三个方面：效率、类型和保存基本类型的能力。

在Java中，数组是一种效率最高的存储和随机访问对象引用序列的方式。数组就是一个简单的线性序列，这使得元素访问非常快速。但是为这种速度付出的代价是数组对象的大小被固定，并且其生命周期中不可改变。

## 16.2 数组是第一级对象

无论是哪一种类型的数组，数组标识符其实是个引用，指向在堆中创建的一个真实对象，这个数组对象用以保存指向其他对象的引用。

## 16.5 数组与泛型

通常，数组与泛型不能很好的结合。你不能实例化具有参数化类型的数组。比如，下面是不允许的：

```java
Peel<Banana>[] peels = new Peel<Banana>[10]; // Illegal
```

但是，你可以参数化数组本身的类型；

```java
class ClassParameter<T> {
    public T[] f(T[] arg) {
        return args;
    }
}
class MethodParameter {
    public static <T> T[] f(T[] arg) {
        return arg;
    }
}
class ParameterizedArrayType {
    public static void main(String[] args) {
        Integer[] ints = {1, 2, 3, 4, 5};
     	Double[] doubles = {1.1, 2.2, 3.3, 4.4, 5.5};
      	Integer[] ints2 = new ClassParameter<Integer>().f(ints);
    }
}
```

使用参数化方法而不是参数化类的方便之处在于：你不必为需要应用的美中不同类型都使用一个参数去实例化这个类，并且你可以将其定义为静态的。当然，你不能总是选择使用参数化方法而不是参数化类，但是参数化方法是首选。

### 16.6.2 数据生成器

为了以灵活的方式创建更有意义的数组，我们将使用15章引入的Generator概念。如果某个工具使用了Generator，那么你就可以通过选择Generator的类型来创建任何类型的数据（这是策略设计模式的一个实例——每个不同的Generator都表示一个不同的策略）。

首先给出的是可以用于所有基本类型的包装器类型，以及String类型的嘴基本的计数生成器集合。这些生成器都嵌套在CountingGenerator类中，从而使得它们能够使用与所要生成的对象类型相同的名字。

```java
public class CountingGenerator {
    public static class Boolean implements Generator<Boolean> {
        private boolean value = false;
      	public Boolean next() {
            value = !value;
          	return value;
        }
    }
  
  	public static class Byte implements Generator<Byte> {
        private byte value = 0;
      	public Byte next() {
            return value++;
        }
    }
}
```

上面的每个类都实现了某种意义的“计数”

## 16.7 Arrays实用功能

`Arrays`类有一套用于数组的static实用方法，其中有六个基本方法：`equals`用于比较两个数组是否相等(`deepEquals`用于多维数组），`fill`用于填充；`sort`用于对数组排序；`binarySearch`用于在已排序的数组中查找元素；`toString`产生数组的String表示；`hashCode`产生数组的散列码。

### 16.7.1 复制数组

Java标准类库提供有static方法`System.arraycopy`，用它复制数组比用for循环要快很多。`System.arraycopy`针对所有类型做了重载。

### 16.7.2 数组的比较

`Arrays`类提供了重载后的`equals`方法，用来比较整个数组。此方法针对所有的基本类型与Object类都做了重载。数组相等的条件是元素个数必须相等。

### 16.7.3 数组元素的比较

排序必须根据对象的实际类型执行比较操作。一种自然的解决方案是为每种不同类型各编写一个不同的排序方法，但是这样的代码难以呗新类型所复用。

程序设计的基本目标是：将不变的食物与会发生变化的事物相分离。而这里，不变的是通用的排序算法，变化的是各种对象相互比较的方式。因此，不是讲进行比较的代码编写成不同的子程序，而是使用策略设计模式。通过使用策略，可以将“会发生变化的代码”封装在单独的类中，你可以将策略对象传递给总是相同的代码，这些代码将使用策略来完成起算法。

Java有两种方式来提供比较功能。第一种是实现`java.lang.Comparable`接口，使你的类具有“天生”的比较能力。此借口很简单，只有compareTo()一个方法。此方法接受另一个Object为参数，如果当前对象小于参数则返回负值，如果相等则返回零，如果当前对象大于参数则返回正直。

### 16.7.3 数组排序

使用内置的排序方法，就可以对任意的基本类型数组进行排序，也可以对任意的对象数组进行排序，只要该对象实现了`Comparable`接口或具有关联的`Comparator`。

```java
String[] sa;
Arrays.sort(sa);
```

Java标准类库中的排序算法针对正排序的特殊类型进行了优化——针对基本类型射击的“快速排序”，以及针对对象设计的“稳定归并排序”。所以无需担心性能问题。

### 16.7.5 在已排序的数组中查找

如果数组已经排好序，就可以使用`Arrays.binarySearch()`执行快速查找。如果要针对未排序的数组使用`binarySearch()`，将产生不可预料的结果。

## 16.8 总结

在本章中，Java对尺寸固定的低级数组提供了适度的支持。这种数组强调的是性能而不是灵活性，并且在与C和C++的数组模型类似。

后续的Java版本对容器的支持得到了明显的改进，并且现在的容器除了性能以外的各个方面都使得数组相形见绌。但是，性能出问题的地方通常是无论如何你都无法想象得到的。

除了额外的自动包装机制和泛型，在容器中持有基本类型就变得易如反掌，这也进一步促使你用容器来替换数组。因为泛型可以产生类型安全的容易，因此数组面对这一变化，已经变的毫无优势了。

