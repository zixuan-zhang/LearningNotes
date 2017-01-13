## 序列化和文件的输入/输出
* 序列化的时候有个serialVersionID。当Java要尝试还原对象的时候，它会对比对象与Java虚拟机类上的serialVersionID。如果版本不符，就会抛出异常。因此解决方案就是把serialVersionID放在class里，让类在演化过程中维持相同的ID。但是这个要非常小心才行。用到的时候应该会比较少。不过还是要注意下。
* 序列化程序会将对象版图上的所有东西存储起来。被对象的实例变量所引用的所有对象都被序列化
* 如果要让类能够被序列化，就实现`Serializable`。`Serializable`接口又被称为marker或tag类的标记接口，因为此接口并没有任何方法需要实现的。它唯一的目的就是生命有实现它的类是可以被序列化的。也就是说，此类型对象可以通过序列化的机制来存储。如果某类可以是序列化的，那么它的子类也自动的可以序列化。`objectOutputStream.writeObject(object)`。任何放在此处的对象都必须要实现序列化接口，否则执行期一定会出现问题。

```
import java.io.*;
public class Box implement Serializable {
    private int width;
    private int height;
    public void setWidth(int w) {
        width = 2;
    }
    public void setHeight(int h) {
        height = h;
    }
    public static void main (String[] args) {
        Box box = new Box();
        box.setWidth(50);
        box.setHeight(20);
        try {
            FileOutputStream fs = new FileOutputStream("foo.ser");
            ObjectOutputStream os = new ObjectOutputStream(fs);
            os.writeObject(box);
            os.close();
        } catch (Exception ex) {
            ex.printStackTrace();
        }
    }
}
```

* 如果某个实例变量不能活不应该被序列化，就把它标记为transient(瞬时)的。此时，序列化程序会把它跳过。
```
import java.net.*;
class Chat implement Serializable {
    transient String currentID;
    string userName;
    // More code.
}
```

### 反序列化
```
FileInputStream fileStream = new FileInputStream("foo.ser");
ObjectInputStream os = new ObjectInputStream(fileStream);
// 读取对象
Object one = os.readObject(); // 每次调用readObject都会从stream中读取下一个对象，读取顺序与写入顺序相同，次数超过会抛异常
Object two = os.readObject();
GameCharacter elf = (GameCharacter) one; // 转换对象类型
os.close();
```
* 当对象被反序列化时，Java虚拟机会通过尝试在堆上创建新的对象，让它维持与被序列化时有相同的状态来恢复对象的原状。但这不包括transient的变量。
* **问题**：如果对象在继承树上有个不可序列化的祖先类，则该不可序列化类以及在它智商的类的构造函数（就算是可序列化也一样）就会执行。一旦构造函数连锁启动之后将无法停止。也就是说，从第一个不可序列化的父类开始，全部都会重新初始状态。
* 对象的实例变量会被还原成序列化时点的状态值。transient变量会被赋值null的对象应用或primitive主数据类型的默认值。
* 静态变量不会被序列化。要记得static代表每个类一个，而不是每个对象一个。当对象被还原时，静态变量会维持类中原本的样子，而不是存储时的样子。==这样不会有问题嘛==

### 文件
和文件相关的类有很多，这个还是用到的时候再学习，现在就简单熟悉下吧。
```
try {
    FileWriter writer = new FileWritr("Foo.txt");
    writer.write("hello foo");
    writer.close();
} catch (IOException ex) {
    ex.printStackTrace();
}
```
#### java.io.File
`File`这个类代表磁盘上的文件，但不是文件中的内容。你可以把File想象成文件的路径，而不是文件本身。`File`并没有读写文件的方法。
* 创建出代表现存盘文件中的File对象： `File f = new File("MyCode.txt");`
* 建立新的目录： `File dir = new File("Chapter7"); dir.mkdir();`
* 列出目录下的内容：
```
if (dir.isDirectory()) {
    String[] dirContents = dir.list();
    for (int i = 0; i < dirContents.length; ++i) {
        System.out.print(dirContents[i]);
    }
}
```
* 取得文件或目录的绝对路径：`dir.getAbsolutePath()`
* 删除文件或目录：`boolean isDeleted = f.delete()`

#### 缓冲区
缓冲区的奥妙之处就在于使用缓冲区比没有缓冲区的效率更好。如果使用`FileWriter.write()`来写的话，每趟磁盘操作都要比内存操作花费更多的时间。而通过`BufferedWriter`和`FileWriter`的连接，`BufferedWriter`可以暂存一堆数据，然后满的时候再写入磁盘，这样就可以减少对磁盘操作的次数。如果想要强制缓冲区立即下乳，只要调用`writer.flush()`这个方法即可。

#### 读取文件
类似的，使用缓冲区会读的更快一点。
```
try {
    File myFile = new File("MyText.txt");
    FileReader fileReader = new FileReader(myFile);
    BufferedReader reader = new BufferedReader(fileReader);
    String line = null;
    while ((line = reader.readLine() != null) {
        // Do something.
    }
    reader.close();
} catch (Exception ex) {
    ex.printStackTrace();
}
