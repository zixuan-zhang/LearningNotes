# 第20章 注解

注解（也被称为元数据）为我们在代码中添加信息提供了一种可视化的方法，使我们可以在稍后的某个时刻非常方便的使用这些数据。

注解在一定程度上把元数据与源代码文件结合在一起，而不是保存在外部文档中这一大趋势之下所催生的。

注解可以提供涌来完整的描述程序所需的信息，这些信息是无法用Java来表达的。因此，注解使得我们能够以将由编译器来测试盒验证的格式，存储有关程序的额外信息。注解可以用来生成描述符文件，甚至或者新的类定义，并且有助于减轻编写”样板“代码的负担。通过使用注解，我们可以将这些元数据保存在源代码中，并且利用annotation API为自己的注解构造处理工具，同时，注解的优点包括：更加干净易读的代码以及编译器类型检查等。

### 20.1.1 定义注解

```java
import java.lang.annotation.*;

@Target(ElementType.METHOD)
@Retention(RetentionPolicy.RUNTIME)
public @interface Test {}
```

除了@符号以外，@Test的定义很像一个空的接口。定义注解时，会需要一些元注解，如`@Target`和`Retention`。`@Target`用来定义你的注解应用于什么地方（例如时一个方法或者一个域）。`Retention`用来定义改注解在哪个级别可用，源代码（Source）中、类文件（CLASS）中还是运行时（RUNTIME）。

在注解中，一般都会包括一些元素以表示某些值。当分析处理注解时，程序或工具可以利用这些值。注解的元素看起来就像接口的方法，唯一区别是你可以为其指定默认值。没有元素的注解被称为标记注解（marker annotation），如上例中的`@Test`。

```Java
@Target(ElementType.METHOD)
@Tetention(RetentionPolicy.RUNTIME)
public @interface UseCase {
  public int id();
  public String description() default "Nothing";
}

public class PasswordUtils{
  @UseCase(id = 47, description="Passwords must contain at least one numeric")
  public boolean validatePassword(String password) {
    return true;
  }
}
```

### 20.1.2 元注解

Java目前只内置了三种标准注解，以及四种元注解。元注解负责注解其他的注解：

| 注解          | 解释                                       |
| ----------- | ---------------------------------------- |
| @Target     | 表示该注解可以用于什么地方。可能的ElementType参数包括：1. CONSTRUCTOR: 构造器声明 2. FIELD: 域声明（包括enum实例） 3. LOCAL_VARIABLE:局部变量声明 4. METHOD:方法声明 5. PACKAGE: 包声明 6. PARAMETER: 参数声明 7. TYPE:类、接口或enum声明 |
| @Retention  | 表示需要在什么级别保存该注解信息。可选的RetentionPolicy包括：1. SOURCE: 注解将被编译器丢弃 2. CLASS: 注解在class文件中游泳，但是会被JVM丢弃 3. RUNTIME:JVM将在运行期也保留注解，因此可以通过反射机制读取注解的信息。 |
| @Documented | 此注解包含在Javadoc中                           |
| @Inherited  | 允许子类集成父类中的注解                             |

## 20.2 编写注解处理器

如果没有用来读取注解的工具，那注解也不会比注释更有用。使用注解的过程中，很重要的一个部分是创建与使用注解处理器。Java SE5扩展了反射机制的API，以帮助程序员构造这类工具。同时，它还提供了一个外部的工具apt帮助程序员解析带有注解的Java源代码。

下面是一个非常简单的注解处理器，使用反射机制查找@UseCase标记。我们为其提供了一组id值，然后它会列出在PasswordUtils中找到的用例。

```java
public class UseCaseTracker {
  public static void trackUseCases(List<Integer> useCases, Class<?> clazz) {
    for (Method m : class.getDeclaredMethods()) {
      UseCase uc = m.getAnnotation(UseCase.class);
      if (uc != null) {
        System.out.println("Found use case: " uc.id());
        useCases.remove(new Integer(uc.id()));
      }
    }
  }
}
```

这个程序用到了两个反射的方法：`getDeclaredMethods()`和`getAnnotation()`，它们都属于AnnotatedElement接口（Class, Method, Field等类都实现了该接口）。`getAnnotation()`方法返回指定类型的注解对象，在这里就是UseCase。

### 20.2.1 注解元素

注解元素可用的类型有：

* 所有基本类型
* String
* enum
* Annotation
* 以上类型的数组

### 20.2.2 默认值限制

编译器对元素的默认值有些过分挑剔。首先，元素不能有不确定的值。也就是说，元素必须要么有默认值，要么在使用注解时提供元素的值。

对于非基本类型的元素，无论在源代码声明时还是在注解接口中定义默认时，都不能以null作为其值。

### 20.2.4 注解不支持继承

