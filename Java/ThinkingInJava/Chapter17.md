# 第17章 容易深入研究

## 17.2 填充容器

容易的填充仍像`java.util.Arrays`一样面临同样的不足。就像`Arrays`一样，相应的`Collections`类也有一些实用的`static`方法，其中包括`fill`。与`Arrays`版本一样，此`fill`方法也只是复制同一个对象引用来填充整个容器的，并且只对`List`对象有用，但是产生的列表可以传递给构造器或者`addAll`方法。

```java
public class FilingLists {
    public static void main(String[] args) {
        List<StringAdress> list = new ArrayList<StringAddress>(Collections.nCopies(4, new StringAddress("Hello")));
      	Collections.fill(list,, new StringAddress("World"));
    }
}
```

这个例子展示了两种用单个对象的引用来填充Collection的方式。

## 17.3 Collection的功能方法

下面的列表列出了可以通过Collection执行的所有操作，因此它们也是可通过Set或List执行的所有操作。Map不是继承自Collection

| Method                                  | 说明                                      |
| --------------------------------------- | --------------------------------------- |
| boolean add(T)                          | 确保容器持有具有泛型类型T的参数。如果没有将此参数添加进容器，则返回false |
| boolean addAll(Collection<? Extends T>) | 添加参数片中的所有元素                             |
| void clear()                            | 移除容器中的所有元素                              |
| boolean contains(T)                     | 是否存在                                    |
| Boolean containsAll(Collection<?>)      | 如果容器持有参数重的所有元素，返回true                   |
| boolean isEmpty()                       | 容器中没有元素时返回true                          |
| Iterator<T> iterator                    | 返回一个Iterator<T>，可以用来遍历容器中的元素            |
| Boolean remove(Object)                  |                                         |
| Boolean retainAll(Collection<T>?)       | 只保存参数中的元素（交集）                           |

## 17.4 可选操作

执行各种不同的添加和移除的方法在Collection接口中都是可选操作。

这是一种很不寻常的接口定义方式。接口是面向对象设计中的契约，它声明”无论你如何选择实现该接口，我保证你可以向接口发送这些消息“。但是可选操作违反了这个非常基本的原则，它声明调用某些方法将不会执行有意义的行为，相反，它们会跑出异常。

如果一个操作时可选的，编译器仍旧会严格要求你只能调用该接口中的方法。这与动态语言不同，动态语言可以在任何对象上调用任何方法，并且可以在运行时发现某个特定调用是否可以工作。另外，将Collection当作参数接受大部分方法只会从盖Collection中读取，而Collection读取方法都不是可选的。

## 17.5 List的功能方法

## 17.6 Set和存储顺序

## 17.7 队列

除了并发应用，Queue在Java SE5中仅有的两个实现是LinkedList和PriorityQueue，它们的差异在于排序行为而不是性能。

### 17.7.1 优先级队列

```java
class ToDoList extends PriorityQueue<ToDoList.ToDoItem> {
    static class ToDoItem implements Comparable<ToDoItem> {
        private char primary;
      	private int secondary;
      	private String item;
      	public ToDoItem(String td, char pri, int sec) {
            primary = pri;
          	secondary = sec;
          	item = td;
        }
      	public int compareTo(ToDoItem arg) {
            if (primary > arg.primary) 
              	return 1;
          	if (primary == arg.primary)
              	if (secondary > arg.secondary)
                  	return 1;
          		else if (secondary == arg.secondary)
                  	return 0;
          	return -1;
        }
    }
}
```

### 17.7.2 双向队列

双向队列就像是一个队列，但是可以在任何一端添加或者移除元素。在`LinkedList`中包含支持双向队列的方法，但是在Java标准库中没有任何显示的用于双向队列的接口。因此，LinkedList无法实现这样的接口，你也无法像前面的实例中转型到Queue那样向上转型到Deque。但是，你可以直接使用组合来创建一个Deque类，并直接从LinkedList中暴露相关的方法。

```java
public class Deque<T> {
  	private LinkedList<T> deque = new LinkedList<T>();
  	public void addFirst(T e) {deque.addFirst(e);}
  	public void addLast(T e) {deque.addLast(e);}
  	public T getFirst() {return deque.getFirst();}
  	public T getLast() {return deque.getLast();}
  	public T removeFirst() {return deque.removeFirst();}
  	public T removeLast() {return deque.removeLast();}
  	public int size() {return deque.size();}
  	public String toString() {return deque.toString();}
  	// Other methods.
}
```

## 17.8 理解Map

映射表的基本思想是它维护的是键值关联，因此可以使用键来查找值。标准的Java类库包含了Map的几种基本实现，包括：`HashMap`, `TreeMap`, `LinkedHashMap`, `WeakHashMap`, `ConcurrentHashMap`, `IdentityHashMap`。它们都有同样的基本接口Map，但是行为特性各不相同，这表现在效率、键值对的保存及呈现次序、对象的保存周期，映射表如何在多线程程序中工作和判定“键”等价的策略等方面。

### 17.8.1 性能

性能是映射表中的一个重要问题，当在get()中使用线性搜索时，执行速度会非常慢，而这正是HashMap提高速度的地方。HashMap使用了特殊的值，称作散列码，来取代对键的缓慢搜索。散列码是“相对唯一”的、用以代码对象的int值，它是通过将该对象的某些信息进行转换而生成的。`hashCode()`是根类Object中的方法，因此所有的Java对象都能产生散列码。HashMap就是使用对象的hashCode()进行快速查询的。

| Map实现             | 特性                                       |
| ----------------- | ---------------------------------------- |
| HashMap           | Map基于散列表的实现。插入和查询“键值对”的开销是固定的。可以通过构造器设置容量和负载因子，以调整容器的性能 |
| LinkedHashMap     | 类似于HashMap，但是迭代遍历它时，取得“键值对”的顺序使其插入次序，或者是最近最少使用（LRU）的次序。只比HashMap慢一点，而在迭代访问时反而更快，因为它用列表维护内部次序。 |
| TreeMap           | 基于红黑树实现，查看“键”和“键值对”时，会被排序。TreeMap的特点在于，所得到的结果时经过排序的。TreeMap时唯一的带有`subMap`的方法的Map，它可以返回一个子树。 |
| WeakHashMap       | 弱键（weak key）映射，允许释放映射所指向对象；这是为解决某类特殊问题而设计的。如果映射之外没有引用指向某个“键”，则此“键”可以被垃圾回收器回收 |
| ConcurrentHashMap | 一种线程安全的Map，它不涉及同步加锁。                     |
| IdentityHashMap   | 使用==代替equals对“键”进行比较的散列映射。               |

对Map中使用的键的要求与Set中的元素要求一样，任何键必须有一个equals方法；如果键被用于散列Map，还必须具有恰当的hashCode()；如果键被用于TreeMap，它必须实现Comparable。

### 17.8.3 LinkedHashMap

为了 提高速度，LinkedHashMap散列化所有的元素，但是在遍历键值对时，却又以元素的插入顺序返回键值对。此外，可以在构造器中设定LinkedHashMap，使之采用基于访问的最少使用（LRU）算法，于是没有被访问过的元素就会出现在队列的前面。对于需要定期清理元素以节省空间的程序来说，此功能使得程序员很容易得以实现。

## 17.9 散列与散列码

当你自己创建用作HashMap的键的类，有可能会忘记在其中放置必须的方法，而这是通常会犯的一个错误。需要合理的编写`hashCode()`和`equals()`函数。`equals()`函数判断当前的键是否与表中存在的键相同。正确的`equals()`方法必须满足5个条件

* 自反性。对任意x，`x.equals(x)`返回true
* 对称性。对任意x和y，，如果`y.equals(x)`返回true，则`x.equals(y)`也返回true
* 传递性。对任意x,y,z。如果有`x.equals(y)`返回true，`y.equals(z)`返回true，则`x.equals(z)`一定返回true.
* 一致性。对任意x和y，如果对象中用于等价比较的信息没有改变，那么无论调用`x.equals(y)`多少次，返回的结果应该保持一致
* 对任何不适null的x，`x.equals(null)`一定返回false

### 17.9.1 理解hashCode()

如果不为键覆盖`hashCode()`和`equals()`方法，那么使用散列的数据结构(`HashSet, HashMap, LinkedHashSet, LinkedHashMap`)就无法正确的处理键。然而，要很好的解决此问题，必须了解这些数据结构的内部构造。

首先，散列的目的在于：想要试用一个对象来查找另一个对象。

### 17.9.2 为速度而散列

散列的价值在于速度：散列使得查询得以快速进行。由于瓶颈位于键的查询速度，因此解决方案之一就是保持键的排序状态。

散列更进一步，它将键保存在某处，以便能够很快找到。存储一组元素最快的数据结构式数组，所以使用它来表示键的信息。但是数组不能调整容量，因此我们希望在Map中保存数量不确定的值，但是如果键的数量被数组容量限制了，，该怎么办呢？

答案就是：**数组并不保存键本身，而是通过键对象生成一个数字，将其作为数组的下标。这个数字就是散列码，由定义在Object中的、且可能由你的类覆盖的`hashCode()`方法**

为解决数组容量被固定的问题，不同的键可以产生相同的下标。也就是说可能会有冲突。因为数组多大就不重要了，任何键总能在数组中找到它的位置。

于是查询一个值的过程首先是计算散列码，然后使用散列码查询数组，如果能够保证没有冲突，那就是一个完美的散列函数，但是这种情况只是特里。通常，冲突由外部链接处理：数组并不直接保存址，而是保存值的list。然后，对list中的值使用equals()方法进行线性的查询。这部分的查询自然会比较慢，但是如果散列函数好的话，数组的每个位置就只有比较少的值。因此，不是查询整个list，而是快速地跳到数组的某个位置，只对很少的元素进行比较。这便是HashMap快的原因。

由于散列表中的槽位（slot）通常被称为桶位（bucket），因此我们将表示实际散列表的数组命名为bucket。为了使散列分布均匀，桶的数量通常使用质数。为了能够自动处理冲突，使用了一个LinkedList的数组；每一个新的元素之时直接添加到list数组。

### 17.9.3 覆盖`hashCode()`

一份像样的`hashCode()`指导：

1. 给int变量result赋予某个非零值常量
2. 为对象每个有意义的域，计算出一个int散列码c
3. 合并计算得到的散列码
4. 返回result
5. 检查hashCode()最后生成的结果，确保相同的对象有相同的散列码

## 17.10 选择接口的不同实现

尽管只有四种容器：`Map, List, Set, Queue`，但是每种接口都有不止一个实现版本。如果需要某种接口的功能，应该如何选择哪一个实现呢？

要根据实现特性，性能进行比较选择。

## 17.11 实用方法

## 17.12 持有引用

