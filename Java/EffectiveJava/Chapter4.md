
# Chapter 4 Classes and Interfaces

## Item 15: Minimize the accessibility of classes and members
使类和成员的可访问性最小化。
A well-designed components hides all its implementation details, cleanly separating its API from its implementation.  信息隐藏或封装，是软建设的基本原则

1. it decouples the components that comprise a system, allowing them to be developed, tested, optimized, used, understood, and modified in isolation. This speeds up system development because components can be developed in parallel. It eases the burden of maintenance because components can be understood more quickly and debugged or replaced with little fear of harming other components.
2. Information hiding increases software reuse
3. information hiding decreases the risk in building large systems because individual components may prove successful even if the system does not

## Item 16: In public classes, use accessor methods, not public fields
如果一个类在其包之外是可访问的，则提供访问方法来保留更改类内部表示的灵活性。 如果一个公共类暴露其数据属性，那么以后更改其表示形式基本没有可能，因为客户端代码可以散步在很多地方。

如果一个雷是包级私有的，或者是一个私有的内部类，那么暴露其属性没有什么本质上的错误——假设它们提供足够描述该类提供的抽象。

## Item 17: Minimize mutability 最小化可变性
不可变类更容易设计，实现和使用。他们不容易出错，更安全.

规则：
1. 不要提供修改对象状态的方法
2. 确保这个类不能被继承
3. 把所有属性设置为final
4. 把所有的属性设置为private
5. 确保对任何可变组件的互斥访问。

## Item 18: Favor composition over inheritance 组合优于继承
原因：与方法调用不同，继承打破了封装。一个子类依赖于其父类的实现细节来确保其功能。如果父类发生变化，那子类可能会被破坏。

只有在子类是真的父类的子类型的情况下，继承才合适。只有在两个类之间存在"is-a"关系的情况下，B才能继承A。问自己个问题：每个B都是A吗？如果不能如实回答这个问题，B就不应该继承A。

如果在适合组合的地方使用继承，则会不必要的公开实现细节。由此产生的API与原始实现联系在一起，永远限制类的性能。

## Item 19: Design and document for inheritance or else prohibit it

## Item 20: Prefer interfaces to abstract classes

## Item 21: Design interfaces for posterity

## Item 22: Use interfaces only to define types

## Item 23: Prefer class hierarchies to tagged classes

## Item 24: Favor static member classes over nonstatic

## Item 25: Limit source files to a single-top-level class
