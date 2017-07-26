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