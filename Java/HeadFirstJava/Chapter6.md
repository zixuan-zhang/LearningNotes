## 认识Java的API

### ArrayList
* `ArrayList.add()`操作是插入的引用还是深拷贝？ ==疑问==
* 一般数组在创建时就必须确定大小，而`ArrayList`则不需要
* 一般数组存放对象时必须指定位置
* 虽然`ArrayList`只能携带对象而不是primitive主数据类型，但是编译器能够自动地将primitive主数据类型包装成object以存放在ArrayList中

### import类
* import与C语言程序中的include并不相同。运用import只是帮助你省下签名的包名称而已。程序不会因为用了import而变大或变慢。
