# Java 方法

[原文地址](http://tutorials.jenkov.com/java-reflection/methods.html)   

作者: Jakob Jenkov 译者:叶文海（yewenhai@gamil.com）

## 内容索引

1. 获取 Method 对象
2. 方法参数以及返回类型
3. 通过 Method 对象调用方法

使用 Java 反射你可以在运行期检查一个方法的信息以及在运行期调用这个方法，通过使用 java.lang.reflect.Method 类就可以实现上述功能。在本节会带你深入了解 Method 对象的信息。


## 获取 Method 对象

可以通过 Class 对象获取 Method 对象，如下例：


```
Class aClass = ...//获取Class对象
Method[] methods = aClass.getMethods();
```

返回的 Method 对象数组包含了指定类中声明为公有的(public)的所有变量集合。   

如果你知道你要调用方法的具体参数类型，你就可以直接通过参数类型来获取指定的方法，下面这个例子中返回方法对象名称是“doSomething”，他的方法参数是 String 类型：

```
Class  aClass = ...//获取Class对象
Method method = aClass.getMethod("doSomething", new Class[]{String.class});
```


如果根据给定的方法名称以及参数类型无法匹配到相应的方法，则会抛出 NoSuchMethodException。
如果你想要获取的方法没有参数，那么在调用 getMethod()方法时第二个参数传入 null 即可，就像这样：


```
Class  aClass = ...//获取Class对象
Method method = aClass.getMethod("doSomething", null);
```

## 方法参数以及返回类型

你可以获取指定方法的方法参数是哪些：


```
Method method = ... //获取Class对象
Class[] parameterTypes = method.getParameterTypes();
```

你可以获取指定方法的返回类型：


```
Method method = ... //获取Class对象
Class returnType = method.getReturnType();
```

## 通过 Method 对象调用方法

你可以通过如下方式来调用一个方法：


```
//获取一个方法名为doSomesthing，参数类型为String的方法
Method method = MyObject.class.getMethod("doSomething", String.class);
Object returnValue = method.invoke(null, "parameter-value1");
```

传入的 null 参数是你要调用方法的对象，如果是一个静态方法调用的话则可以用 null 代替指定对象作为 invoke()的参数，在上面这个例子中，如果 doSomething 不是静态方法的话，你就要传入有效的 MyObject 实例而不是 null。
 Method.invoke(Object target, Object … parameters)方法的第二个参数是一个可变参数列表，但是你必须要传入与你要调用方法的形参一一对应的实参。就像上个例子那样，方法需要 String 类型的参数，那我们必须要传入一个字符串。

本文链接地址: [Java Reflection(五):方法](http://ifeve.com/java-reflection%E4%BA%94%E6%96%B9%E6%B3%95/)
