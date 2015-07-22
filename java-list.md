# Java 数组

[原文地址](http://tutorials.jenkov.com/java-reflection/arrays.html) 

作者: Jakob Jenkov 译者:叶文海（yewenhai@gmail.com）

## 内容索引

1. java.lang.reflect.Array
2. 创建一个数组
3. 访问一个数组
4. 获取数组的 Class 对象
5. 获取数组的成员类型

利用反射机制来处理数组会有点棘手。尤其是当你想要获得一个数组的 Class 对象，比如 int[] 等等。本节会讨论通过反射机制创建数组和如何获取数组的 Class 对象。

注意：在阅读 Eyal Lupu 的博客文章“[Two Side Notes About Arrays and Reflection](http://jroller.com/eyallupu/entry/two_side_notes_about_arrays)”之后对本文的内容做了更新。目前这个版本参考了这篇博文里面的内容。


## java.lang.reflect.Array

Java 反射机制通过 java.lang.reflect.Array 这个类来处理数组。不要把这个类与 Java 集合套件（Collections suite）中的 java.util.Arrays 混淆， java.util.Arrays 是一个提供了遍历数组，将数组转化为集合等工具方法的类。

## 创建一个数组

Java 反射机制通过 java.lang.reflect.Array 类来创建数组。下面是一个如何创建数组的例子：

```
int[] intArray = (int[]) Array.newInstance(int.class, 3);
```

这个例子创建一个 int 类型的数组。Array.newInstance()方法的第一个参数表示了我们要创建一个什么类型的数组。第二个参数表示了这个数组的空间是多大。

## 访问一个数组   

通过 Java 反射机制同样可以访问数组中的元素。具体可以使用Array.get(…)和Array.set(…)方法来访问。下面是一个例子：

```
int[] intArray = (int[]) Array.newInstance(int.class, 3);

Array.set(intArray, 0, 123);
Array.set(intArray, 1, 456);
Array.set(intArray, 2, 789);

System.out.println("intArray[0] = " + Array.get(intArray, 0));
System.out.println("intArray[1] = " + Array.get(intArray, 1));
System.out.println("intArray[2] = " + Array.get(intArray, 2));
```

这个例子会输出：

```
intArray[0] = 123
intArray[1] = 456
intArray[2] = 789
```

## 获取数组的 Class 对象

在我编写 Butterfly DI Container 的脚本语言时，当我想通过反射获取数组的 Class 对象时遇到了一点麻烦。如果不通过反射的话你可以这样来获取数组的 Class 对象：

```
Class stringArrayClass = String[].class;
```

如果使用 Class.forName()方法来获取 Class 对象则不是那么简单。比如你可以像这样来获得一个原生数据类型（primitive）int 数组的 Class 对象：

```
Class intArray = Class.forName("[I");
```

在 JVM 中字母 I 代表 int 类型，左边的‘[’代表我想要的是一个int类型的数组，这个规则同样适用于其他的原生数据类型。
对于普通对象类型的数组有一点细微的不同：

```
Class stringArrayClass = Class.forName("[Ljava.lang.String;");
```

注意‘[L’的右边是类名，类名的右边是一个‘;’符号。这个的含义是一个指定类型的数组。
需要注意的是，你不能通过 Class.forName()方法获取一个原生数据类型的 Class 对象。下面这两个例子都会报 ClassNotFoundException：

```
Class intClass1 = Class.forName("I");
Class intClass2 = Class.forName("int");
```

我通常会用下面这个方法来获取普通对象以及原生对象的 Class 对象：

```
public Class getClass(String className){
  if("int" .equals(className)) return int .class;
  if("long".equals(className)) return long.class;
  ...
  return Class.forName(className);
}
```

一旦你获取了类型的 Class 对象，你就有办法轻松的获取到它的数组的 Class 对象，你可以通过指定的类型创建一个空的数组，然后通过这个空的数组来获取数组的 Class 对象。这样做有点讨巧，不过很有效。如下例：

```
Class theClass = getClass(theClassName);
Class stringArrayClass = Array.newInstance(theClass, 0).getClass();
```

这是一个特别的方式来获取指定类型的指定数组的Class对象。无需使用类名或其他方式来获取这个 Class 对象。
为了确保 Class 对象是不是代表一个数组，你可以使用 Class.isArray()方法来进行校验：

```
Class stringArrayClass = Array.newInstance(String.class, 0).getClass();
System.out.println("is array: " + stringArrayClass.isArray());
```

## 获取数组的成员类型

一旦你获取了一个数组的 Class 对象，你就可以通过 Class.getComponentType()方法获取这个数组的成员类型。成员类型就是数组存储的数据类型。例如，数组 int[]的成员类型就是一个 Class 对象 int.class。String[]的成员类型就是 java.lang.String 类的 Class 对象。
下面是一个访问数组成员类型的例子：

```
String[] strings = new String[3];
Class stringArrayClass = strings.getClass();
Class stringArrayComponentType = stringArrayClass.getComponentType();
System.out.println(stringArrayComponentType);
```

下面这个例子会打印“java.lang.String”代表这个数组的成员类型是字符串。


本文链接地址: [Java Reflection(十):数组](http://ifeve.com/java-reflection-10-arrays/)