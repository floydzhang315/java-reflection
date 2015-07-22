# Java 构造器

[原文地址](http://tutorials.jenkov.com/java-reflection/constructors.html) 作者: Jakob Jenkov 译者:叶文海（yewenhai@gamil.com）

## 内容索引

1. 获取 Constructor 对象
2. 构造方法参数
3. 利用 Constructor 对象实例化一个类

利用 Java 的反射机制你可以检查一个类的构造方法，并且可以在运行期创建一个对象。这些功能都是通过 java.lang.reflect.Constructor 这个类实现的。本节将深入的阐述 Java Constructor 对象。


## 获取 Constructor 对象

我们可以通 过Class 对象来获取 Constructor 类的实例：


```
Class aClass = ...//获取Class对象
Constructor[] constructors = aClass.getConstructors();
```


返回的 Constructor 数组包含每一个声明为公有的（Public）构造方法。
如果你知道你要访问的构造方法的方法参数类型，你可以用下面的方法获取指定的构造方法，这例子返回的构造方法的方法参数为 String 类型：


```
Class aClass = ...//获取Class对象
Constructor constructor =
  aClass.getConstructor(new Class[]{String.class});
```

  
如果没有指定的构造方法能满足匹配的方法参数则会抛出：NoSuchMethodException。

## 构造方法参数

你可以通过如下方式获取指定构造方法的方法参数信息：

```
Constructor constructor = ... //获取Constructor对象
Class[] parameterTypes = constructor.getParameterTypes();
```

## 利用 Constructor 对象实例化一个类

你可以通过如下方法实例化一个类：

```
Constructor constructor = MyObject.class.getConstructor(String.class);
MyObject myObject = (MyObject)
 constructor.newInstance("constructor-arg1");
```
constructor.newInstance()方法的方法参数是一个可变参数列表，但是当你调用构造方法的时候你必须提供精确的参数，即形参与实参必须一一对应。在这个例子中构造方法需要一个 String 类型的参数，那我们在调用 newInstance 方法的时候就必须传入一个 String 类型的参数。

原创文章，转载请注明： 转载自[并发编程网 – ifeve.com](http://ifeve.com/)

本文链接地址: [Java Reflection(三):构造器](http://ifeve.com/java-reflection-constructors/)