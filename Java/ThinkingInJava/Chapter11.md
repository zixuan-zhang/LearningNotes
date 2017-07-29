# 第11章 持有对象

Java实用类库提供了完整的容器类来解决持有对象这个问题，其中基本类型是List、Set、Queue、Map。这些对象类型被称为集合类，但是由于Java类库实用Collection这个名字来指代该类库的一个特殊自己，所以用“容器”来称呼它们。

本章将了解Java容器类库基本知识。

## 11.1 泛型和类型安全的容器

例如ArrayList即支持泛型，如下面的例子

```java
ArrayList<Apple> apples = new ArrayList<>();
apples.add(new Apple()); // Good
apples.add(new Orange()); // Compile Error
Apple apple = apples.get(0); // No need for type cast.
```

上面的例子中`ArrayList.get()`不需要转型，因为它自动转型了。包括向上转型。

## 11.2 基本概念

1. **Collection**

一个独立元素序列，这些元素都服从一条或多条规则。List必须按照插入的顺序保存元素，而Set不能有重复的元素。Queue按照排队规则来确定对象产生的顺序。

2. **Map**

键值和对象相关联。

所有的Collection都可以用foreach语法遍历。后面会用到迭代器。

## 11.3 添加一组元素

* `Arrays.asList()`：接受一个数组或是一个用逗号分隔的元素列表（使用可变参数），并将其转换为一个List对象
* `Collections.addAll()`：接受一个Collection对象，以及一个数组或是一个用逗号分隔的列表，将元素添加到Collection中

下面给出一个实例，还挺常用的：

```java
public class AddingGroup{
  public static void main(String[] args) {
    Collection<Integer> collection = new ArrayList<Integer>(Arrays.asList(1, 2, 3, 4, 5));
    Integer[] moreInts = {6, 7, 8, 9};
    collection.addAll(Arrays.asList(moreInts));
    
    // Produces a list "backed by" an array
    List<Integer> list = Arrays.asList(11, 12, 13);
    list.set(1, 99); // OK --modify an element
    // list.add(21); // Runtime error because the underlying array cannot be resized.
  }
}
```

Collection构造器可以接受另一个Collection来将自身初始化，因此可以使用`Array.List()`为这个构造器产生输入。`Collection.addAll()`方法只能接受另一个Collection对象作为参数，因此它不如`Arrays.asList()`灵活，因为使用的是可变参数列表。

也可以直接使用`Arrays.asList()`的输出，将其当作List，但是其底层表示是数组，因此不能调整尺寸。

## 11.4 容器的打印

使用容器的toStirng()函数，就能得到很好的打印效果。

## 11.5 List

List承诺将元素维护在特定的序列中。List接口在Collection的基础上添加了大量的方法，使得可以在List中间插入和移除元素。

* 几本的ArrayList。它长于随机访问，但是在List的中间插入和移除元素事较慢。
* LinkedList，它通过代价较低在List中间进行插入和删除操作，提供了优化的顺序访问。LinkedList在随机访问方面相对较慢。

下面是一些List相关的方法

* `subList()`：允许从较大的列表中创建出一个片段，
* `retainAll()`：是一种有效的“交集”操作
* `removeAll()`：将从List中移除在参数List中的所有参数
* `set()`:它的功能室仔指定的索引处，用第二个参数替换那个位置的元素。

## 11.6 迭代器

任何容器类都必须有某种方式可以插入元素并将它们再次取回。有时候，我们只是使用容器，**并不关心容器的类型**，如何重写代码可以应用于不同类型的容器？

迭代器（也是一种设计模式）的概念可以用于达成此目的。迭代器是一个对象，它的工作是遍历柄选择序列中的对象。此外，迭代器通常被称为轻量级对象：创建它的代价小。

1. 使用方法`iterator()`要求容器返回一个`Iterator`，将准备好返回蓄力的第一个元素。

2. 使用`next()`获得序列中的下一个元素。

3. 使用`hasNext()`检查序列中是否还有元素。

4. 使用`remove()`将迭代器新近返回的元素删除。即

   ````java
   Iterator<Object> it=List<Object>.iterator().next(); it.remove()
   ````

### 11.6.1 ListIterator

`ListIterator`是一个更强大的`Iterator`子类型，它只能用于各种List类的访问。尽管`Iterator`只能向前移动，但是`ListIterator`可以双向移动。它还可以产生相对于迭代器在列中中指向的当前位置的前一个和后一个元素的索引。

## 11.7 LinkedList

`LinkedList`还添加了使其用作栈、队列或双端队列的方法。

## 11.8 Stack

“栈”通常是指“后进先出”的容器。LinkedList具有能够直接实现栈的所有功能和方法，因此可以直接将LinkedList作为栈使用。

```java
public class Stack<T> {
  private LinkedList<T> storage = new LinkedList<T>();
  public void push(T v) { storage.addFirst(v);}
  public T peek() { return storage.getFirst();}
  public T pop() { return storage.removeFirst();}
  public bollean empty() {return storage.isEmpty();}
  public String toString() {return storage.toString();}
}
```

## 11.9 Set

Set不保存重复的元素（至于如何判断元素相同则较为复杂）。如果你试图将相同的对象的多个实例添加到Set中，那么它就会阻止这种重复现象。Set中最常用的是查询某个对象是否存在于Set中，因此通常会选择一个HashSet的实现。Set中对象的是无序的，这时因为出于速度原因的考虑，HashSet使用了散列。HashSet所维护的顺序与TreeSet或LinkedHashSet都不同，因为它们的实现具有不同的元素存储方式。TreeSet将元素存储在红黑树中，而HashSet使用的是散列函数。LinkedHashSet因为查询速度的原因也使用了散列，但是看起来它使用了链表来维护元素的插入顺序。**如果想对结果排序，一种方式是使用TreeSet来替代HashSet**

## 11.10 Map

## 11.11 Queue

* offer()方法将一个元素插入到队尾，或者返回false
* peek()和element()都将在不移除的情况下返回队头。但是peek()方法在队列为空时返回null，而element()会抛出NoSuchElementException()
* poll()和remove()方法将移除柄返回队头，但是poll()在队列为空时返回null，而remove()会抛出NoSuchElementException

### 11.11.1 PriorityQueue

优先级队列声明下一次弹出元素是最需要的元素（具有最高优先级）。当在PriorityQueue上调用offer()方法插入一个对象时，这个对象会在队列中被排序，你可以提供自己的Comparator来修改顺序。

## 11.12 Collection和Iterator

Collection是描述所有序列容器的共性的根接口，它可能会被认为是一个“附属接口”，即因为要表示其他若干接口共性和实现的接口。

## 11.13 Foreach与迭代器

foreach语法主要用于数组，但是也应用于任何Collection对象。

这里有适配器的概念，没有太明白。

