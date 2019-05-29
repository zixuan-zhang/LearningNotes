
# Chapter 3 Methods Common to All Objects
本章论述怎样重写`Object`类的非final方法。

## Item 10: Obey the general contract when overriding equals
`equal()`方法必须满足：
* 每个类的实例都是固有唯一的。对于像Thread这样代表活动实体而不是值得类来说，这是正确的。`Object`提供的`equals`实现对这些类完全是正确的行为。
* 类不需要提供一个逻辑相等的测试功能。
* 父类已经重写了equals方法，则父类行为完全适合该自雷。
* 类是私有的或包级私有的，可以确定它的equals方法永远不会被调用。

重写equals方法，必须遵守：
* 自反性。对于任何非空x, `x.equals(x)`必须返回true
* 对称性。对于任何费控x和y，如果当且仅当`y.equals(x)`返回true时，`x.equals(y)`必须返回true
* 传递性。x，y，z的传递性
* 一致性。对于任何非空引用x和y。如果在equals比较中使用的信息没有修改，则x.equals(y)的多次调用必须使用返回true或false
* 对于任何非空引用x，`x.equals(null)`必须返回false
> 所以其实UT可以按照这个来写

综合起来，以下是编写高质量的equals方法的秘方：
1. 使用`==`运算符检查参数是否为该对象的引用。如果是，返回true。这只是一种性能优化。如果equals操作比较昂贵的话，就值得去做。
2. 使用 `instanceof`运算符来检查参数是否具有正确的类型。如果不是，返回false。
3. 参数转换为正确的类型。因为转换操作已经在`instanceof`中处理过，所以肯定会成功。
4. 对于类中每个重要的属性，检查该参数属性是否与该对象属性匹配。如果都测试成功，返回true，否则返回false。

对于非float或double的基本类型，使用==运算符比较；对于对象引用属性，地柜的调用equals方法；对于float基本类型的属性，使用静态Float.compare(float, float)方法；对于double基本属性，使用Double.compare(double, double)方法。虽然你可以使用静态方法Float.equals()和Double.equals方法进行比较，但这回导致每次比较时发生自动装箱，引发非常差的性能。对于数组属性，将这些准则应用于每个元素。

一些提醒：
1. 当重写equals时，也要重写hashcode方法
2. 不要让equals方法试图太聪明。
3. 在equal方法声明时，不要将参数Object替换成其他类型。

Google的AutoValue和Lombok有一些annotation可以自动生成。

## Item 11: Always override hashCode when you override equals
在重写equals方法的时候，一定要重写hashcode方法。
* 如果两个对象根据equals方法比较是相等的，那么两个对象上调用hashCode方法也必须是相同的。
* 如果两个对象根据equals方法比较不相等，则不要求每个对象上调用hashCode都必须产生不同的结果。但是为不相等的对象生成相同的hashCode对降低散列表的性能。

当未重写hashCode时，就违反了：相等的对象必须具有相等的hashcode。

一个好的hash方法趋于未不相等的实例生成不相等的hashcode。理想情况下，hash方法为集合中不相等的实例均匀的分配int范围内的哈希码。但这可能比较难，但是合理的近似方法并不难。
1. 声明一个int类型的变量result，并将其初始化为对象中第一个重要属性c的哈希码。
2. 对于对象中剩余的重要属性f，执行以下操作：
  1. 比较属性f与属性c的int类型的哈希码:
    1. 如果这个属性是基本类型的，使用Type.hashCode(f)方法计算，其中Type类型是f基本类型的包装类
    2. 如果该属性是一个对象引用，并且该类的equals方法通过地柜调用equals来比较该属性，并递归的调用hashcode方法。
    3. 如果属性f是一个数组，把他看做每个重要的元素都是一个独立的属性。
  2. 将步骤2.1中属性f计算出来的哈希码c合并为：result = 31 * result + c

之所以选择31，是因为它是一个奇数的素数。如果它是偶数，并且惩罚溢出，信息就会丢失。使用素数的好处不明显，但是习惯都这么做。31的一个很好的特性，是在一些体系结构中乘法可以被替换为移位和减法以获得更好的性能 31 * i = (i << 5) - i。JVM可以自动进行这种优化。

Objects类有个静态方法，接受任意数量的对象并为它们返回哈希码。但是，这个运行速度更慢。因为它们需要创建数组以传递可变数量的参数，以及可能的装箱操作。

如果一个类是不可变的，并且计算哈希代价比较大，可以尝试缓存。

## Item 12: Always override toString
始终重写`toString()`方法

## Item 13: Override clone judiciously
谨慎的重写`clone`方法

## Item 14: Consider implementing Comparable
