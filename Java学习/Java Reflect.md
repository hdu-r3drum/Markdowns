# class对象？

在 Java 中，`Class` 类型的对象是由 `java.lang.Class` 类的实例所表示的。每当我们编写并编译一个新类时，Java 编译器会为该类生成一个对应的 `Class` 对象，并将其保存在同名的 `.class` 文件中。当程序运行时，Java 虚拟机（JVM）通过类加载器将这些 `Class` 对象加载到内存中。需要注意的是，无论我们创建多少个该类的实例对象，在 JVM 中都只有一个与之对应的 `Class` 对象。 

`Class` 对象主要包含以下信息：

1. **类的名称**：包括完整的包名和类名。
2. **类的修饰符**：如 `public`、`abstract`、`final` 等。
3. **包信息**：该类所属的包。
4. **类的直接超类（父类）**：即该类继承的父类。
5. **实现的接口**：该类实现的所有接口。
6. **字段（成员变量）信息**：包括字段的名称、类型、修饰符等。
7. **方法信息**：包括方法的名称、返回类型、参数类型、修饰符等。
8. **构造方法信息**：包括构造方法的参数类型、修饰符等。
9. **注解信息**：该类上所使用的注解。



# JVM如何保证Class对象的唯一性？

在 Java 中，每个类在运行时都会由 Java 虚拟机（JVM）创建一个唯一的 `Class` 对象来表示该类的元数据。这意味着对于同一个类，无论创建多少实例，都共享同一个 `Class` 对象。JVM 通过类加载器（ClassLoader）机制来确保这一点。

当需要加载一个类时，JVM 首先检查该类是否已经被加载：

1. **类加载检查**：JVM 会查询当前的类加载器是否已加载过该类。如果已加载，则直接返回该类的 `Class` 对象。
2. **类加载过程**：如果类尚未加载，类加载器会读取对应的字节码文件（`.class` 文件），然后将其转换为 `Class` 对象并存储在方法区（在较新的 JVM 实现中，方法区可能被称为元空间）。
3. **缓存机制**：类加载器通常会维护一个缓存，记录已经加载的类。这样可以确保对于同一个类，只加载一次并生成一个 `Class` 对象。



# 获取class对象的三种方式

- Class.forName("全类名")
- 类名.class
- 对象.getClass()



# 实例化

在 Java 9 及更高版本中，`Class.newInstance()` 方法已被弃用。 官方推荐的替代方案是使用 `Class.getDeclaredConstructor().newInstance()` 来创建类的实例。 [OpenJDK Bugs](https://bugs.openjdk.org/browse/JDK-8264138?utm_source=chatgpt.com)

**示例代码：**

```java
try {
    // 获取类的 Class 对象
    Class<?> clazz = Class.forName("com.example.MyClass");

    // 获取无参构造方法
    Constructor<?> constructor = clazz.getDeclaredConstructor();

    // 创建实例
    Object instance = constructor.newInstance();
} catch (ClassNotFoundException | NoSuchMethodException |
         InstantiationException | IllegalAccessException |
         InvocationTargetException e) {
    e.printStackTrace();
}
```

**注意事项：**

- **访问权限**：`getDeclaredConstructor()` 可获取包括私有构造方法在内的所有构造方法。如果需要访问私有构造方法，需在调用 `newInstance()` 之前设置 `constructor.setAccessible(true)`。
- **异常处理**：与 `Class.newInstance()` 不同，`Constructor.newInstance()` 会将被调用构造方法抛出的受检异常（checked exception）包装在 `InvocationTargetException` 中，因此需要适当处理这些异常。



# Field获取

**获取字段对象：**

1. **获取公共字段：**
   - `getField(String name)`: 获取指定名称的 `public` 字段，包括从父类继承的公共字段。
   - `getFields()`: 获取所有 `public` 字段，包括从父类继承的公共字段，返回一个 `Field` 数组。
2. **获取所有声明的字段：**
   - `getDeclaredField(String name)`: 获取当前类中指定名称的字段，不受访问修饰符限制，但不包括继承的字段。
   - `getDeclaredFields()`: 获取当前类中所有声明的字段，返回一个 `Field` 数组，不包括继承的字段。

**示例代码：**

```java
public class ReflectFieldExample {
    public static void main(String[] args) throws Exception {
        Class<?> clazz = SampleClass.class;

        // 获取所有公共字段
        Field[] publicFields = clazz.getFields();
        for (Field field : publicFields) {
            System.out.println("Public Field: " + field.getName());
        }

        // 获取所有声明的字段
        Field[] declaredFields = clazz.getDeclaredFields();
        for (Field field : declaredFields) {
            System.out.println("Declared Field: " + field.getName());
        }

        // 获取指定名称的公共字段
        Field publicField = clazz.getField("publicField");
        System.out.println("Specific Public Field: " + publicField.getName());

        // 获取指定名称的私有字段
        Field privateField = clazz.getDeclaredField("privateField");
        privateField.setAccessible(true); // 设置可访问
        System.out.println("Specific Private Field: " + privateField.getName());
    }
}

class SampleClass {
    public String publicField;
    private int privateField;
}
```



**注意事项：**

- 使用 `getDeclaredField(s)` 获取私有字段时，需要调用 `setAccessible(true)` 来绕过访问控制，以便读取或修改私有字段的值。
- 反射操作可能会破坏封装性，导致安全风险，使用时需谨慎。
- 在获取继承自父类的字段时，`getFields()` 可以获取到公共字段，但 `getDeclaredFields()` 仅获取当前类声明的字段，不包括父类的字段。

## 获取对应值

1. **获取 `Class` 对象**：通过对象的 `getClass()` 方法或直接使用类名 `.class` 获取。
2. **获取 `Field` 对象**：使用 `Class` 对象的 `getDeclaredField(String name)` 方法获取指定名称的字段，或使用 `getDeclaredFields()` 获取所有声明的字段。
3. **设置可访问性**：对于私有字段，需要调用 `setAccessible(true)` 以绕过访问权限检查。
4. **获取字段值**：使用 `Field` 对象的 `get(Object obj)` 方法获取指定对象中该字段的值。

**示例代码：**

```java
import java.lang.reflect.Field;

public class ReflectFieldValueExample {
    public static void main(String[] args) throws Exception {
        // 创建示例对象
        SampleClass sample = new SampleClass("John Doe", 30);

        // 获取 Class 对象
        Class<?> clazz = sample.getClass();

        // 获取公共字段
        Field publicField = clazz.getField("publicField");
        System.out.println("Public Field Value: " + publicField.get(sample));

        // 获取私有字段
        Field privateField = clazz.getDeclaredField("privateField");
        privateField.setAccessible(true); // 设置可访问
        System.out.println("Private Field Value: " + privateField.get(sample));
    }
}

class SampleClass {
    public String publicField = "Public Value";
    private int privateField = 42;

    public SampleClass(String name, int age) {
        this.publicField = name;
        this.privateField = age;
    }
}
```



**注意事项：**

- **异常处理**：上述方法可能抛出 `NoSuchFieldException`、`IllegalAccessException` 等异常，需要适当处理。
- **性能影响**：反射操作通常比直接访问字段慢，频繁使用可能影响性能。
- **安全性**：修改私有字段的可访问性可能违反封装原则，需谨慎使用。