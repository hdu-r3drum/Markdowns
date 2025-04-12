# 什么是动态代理？

在 Java 中，“dynamic proxy” 通常指的是通过 Java 的 java.lang.reflect.Proxy 类在运行时动态生成代理类的机制。它允许你在运行时创建实现了一组接口的代理对象，并定义这些接口方法的处理方式。

**无侵入式的增强对象的功能**

**Java通过接口来保证代理的样子**

```java
import java.lang.reflect.InvocationHandler;
import java.lang.reflect.Method;
import java.lang.reflect.Proxy;

interface HelloService {
    void sayHello();
}

class HelloServiceImpl implements HelloService {
    public void sayHello() {
        System.out.println("Hello!");
    }
}

class MyInvocationHandler implements InvocationHandler {
    private Object target;

    public MyInvocationHandler(Object target) {
        this.target = target;
    }

    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        System.out.println("Before method call");
        Object result = method.invoke(target, args);
        System.out.println("After method call");
        return result;
    }
}

public class DynamicProxyExample {
    public static void main(String[] args) {
        HelloService target = new HelloServiceImpl();
        HelloService proxy = (HelloService) Proxy.newProxyInstance(
            HelloService.class.getClassLoader(),
            new Class[] { HelloService.class },
            new MyInvocationHandler(target)
        );

        proxy.sayHello();  // 输出 Before -> Hello! -> After
    }
}
```

