# Java 变量

[原文地址](http://tutorials.jenkov.com/java-reflection/fields.html) 作者: Jakob Jenkov 译者:叶文海（yewenhai@gamil.com）

## 内容索引

1. 获取 Field 对象
2. 变量名称
3. 变量类型
4. 获取或设置（get/set）变量值

使用 Java 反射机制你可以运行期检查一个类的变量信息(成员变量)或者获取或者设置变量的值。通过使用 java.lang.reflect.Field 类就可以实现上述功能。在本节会带你深入了解 Field 对象的信息。



## 获取 Field 对象

可以通过 Class 对象获取 Field 对象，如下例：

```
Class aClass = ...//获取Class对象
Field[] methods = aClass.getFields();
```

返回的 Field 对象数组包含了指定类中声明为公有的(public)的所有变量集合。
如果你知道你要访问的变量名称，你可以通过如下的方式获取指定的变量：

```
Class  aClass = MyObject.class
Field field = aClass.getField("someField");
```

上面的例子返回的Field类的实例对应的就是在 MyObject 类中声明的名为 someField 的成员变量，就是这样：

```
public class MyObject{
  public String someField = null;
}
```

在调用 getField()方法时，如果根据给定的方法参数没有找到对应的变量，那么就会抛出 NoSuchFieldException。


## 变量名称

一旦你获取了 Field 实例，你可以通过调用 Field.getName()方法获取他的变量名称，如下例：

```
Field field = ... //获取Field对象
String fieldName = field.getName();
```

## 变量类型

你可以通过调用 Field.getType()方法来获取一个变量的类型（如String, int等等）

```
Field field = aClass.getField("someField");
Object fieldType = field.getType();
```

## 获取或设置（get/set）变量值

一旦你获得了一个 Field 的引用，你就可以通过调用 Field.get()或 Field.set()方法，获取或者设置变量的值，如下例：

```
Class  aClass = MyObject.class
Field field = aClass.getField("someField");

MyObject objectInstance = new MyObject();

Object value = field.get(objectInstance);

field.set(objetInstance, value);
```


传入 Field.get()/Field.set()方法的参数 objetInstance 应该是拥有指定变量的类的实例。在上述的例子中传入的参数是 MyObjec t类的实例，是因为 someField 是 MyObject 类的实例。
如果变量是静态变量的话(public static)那么在调用 Field.get()/Field.set()方法的时候传入 null 做为参数而不用传递拥有该变量的类的实例。(译者注：你如果传入拥有该变量的类的实例也可以得到相同的结果)

原创文章，转载请注明： 转载自[并发编程网 – ifeve.com](http://ifeve.com/)

本文链接地址: [Java Reflection(四):变量](http://ifeve.com/java-reflection-fields/)
