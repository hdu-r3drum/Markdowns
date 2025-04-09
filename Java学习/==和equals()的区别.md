在 Java 中，`==` 运算符和 `equals()` 方法都用于比较对象，但它们的作用和适用场景有所不同：

1. **基本数据类型的比较**：
    - `**==**`** 运算符**：用于比较基本数据类型的值是否相等。例如：

```java
int a = 5;
int b = 5;
System.out.println(a == b); // 输出 true
```

    - `**equals()**`** 方法**：基本数据类型没有 `equals()` 方法，因此不能使用。
+ **引用数据类型的比较**：
+ `**==**`** 运算符**：比较两个引用是否指向同一个对象，即比较内存地址。例如：

```java
String str1 = new String("hello");
String str2 = new String("hello");
System.out.println(str1 == str2); // 输出 false
```

在上述代码中，`str1` 和 `str2` 虽然内容相同，但它们指向不同的对象，因此 `==` 比较结果为 `false`。

+ `**equals()**`** 方法**：用于比较对象的内容是否相同。许多类（如 `String`、`Integer` 等）重写了 `equals()` 方法，以比较对象的内容而非引用。例如：

```java
String str1 = new String("hello");
String str2 = new String("hello");
System.out.println(str1.equals(str2)); // 输出 true
```

在上述代码中，`str1` 和 `str2` 的内容相同，因此 `equals()` 比较结果为 `true`。

+ **自定义类的比较**：
+ 如果自定义的类未重写 `equals()` 方法，则默认继承自 `Object` 类，其 `equals()` 方法等同于 `==`，比较的是对象的引用。
+ 为比较自定义对象的内容，需要在类中重写 `equals()` 方法。例如：

```java
public class Person {
    private String name;
    private int age;

    // 构造方法、getter 和 setter 略

    @Override
    public boolean equals(Object obj) {
        if (this == obj) return true;
        if (obj == null || getClass() != obj.getClass()) return false;
        Person person = (Person) obj;
        return age == person.age && Objects.equals(name, person.name);
    }
}
```

通过重写 `equals()` 方法，可以根据对象的属性值来判断对象是否相等。

<font style="color:rgb(44, 62, 80);">“==”运算符比较的时候，如果两个对象都为 null，并不会发生</font><font style="color:rgb(44, 62, 80);"> </font>`<font style="color:rgb(44, 62, 80);">NullPointerException</font>`<font style="color:rgb(44, 62, 80);">，而</font><font style="color:rgb(44, 62, 80);"> </font>`<font style="color:rgb(44, 62, 80);">equals()</font>`<font style="color:rgb(44, 62, 80);"> </font><font style="color:rgb(44, 62, 80);">方法则会。</font>

<font style="color:rgb(44, 62, 80);">另外， “==”运算符会在编译时进行检查，如果两侧的类型不匹配，会提示错误，而 </font>`<font style="color:rgb(44, 62, 80);">equals()</font>`<font style="color:rgb(44, 62, 80);"> 方法则不会。</font>

