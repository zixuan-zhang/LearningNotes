## 编写程序

### 加强版for循环
```
for (String name : nameArray) {    
}
```
上面这段程序以中文来说就是：对nameArray中的每个元素都执行一次，而编译器会这样认为：
1. 创建名为name的String变量
2. 将nameArray的第一个元素值赋给name
3. 执行循环体
4. 赋值给下一个元素name
5. 重复执行至所有元素都被执行

所以如果数组中的是对象的引用，那么在循环中可以对原始对象进行改变。
