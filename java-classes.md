# java 类

[原文地址](http://tutorials.jenkov.com/java-reflection/classes.html)    

作者: Jakob Jenkov  译者:叶文海（yewenhai@gamil.com）


使用 Java 反射机制可以在运行时期检查 Java 类的信息，检查 Java 类的信息往往是你在使用 Java 反射机制的时候所做的第一件事情，通过获取类的信息你可以获取以下相关的内容：

1. Class 对象
2. 类名
3. 修饰符
4. 包信息
5. 父类
6. 实现的接口
7. 构造器
8. 方法
9. 变量
10. 注解

除了上述这些内容，还有很多的信息你可以通过反射机制获得，如果你想要知道全部的信息你可以查看相应的文档 [JavaDoc for java.lang.Class](http://java.sun.com/javase/6/docs/api/java/lang/Class.html) 里面有详尽的描述。


在本节中我们会简短的涉及上述所提及的信息，上述的一些主题我们会使用单独的章节进行更详细的描述，比如这段内容会描述如何获取一个类的所有方法或者指定方法，但是在单独的章节中则会向你展示如何调用反射获得的方法(Method Object)，如何在多个同名方法中通过给定的参数集合匹配到指定的方法，在一个方法通过反射机制调用的时候会抛出那些异常？如何准确的获取 getter/setter 方法等等。本节的内容主要是介绍 Class 类以及你能从Class类中获取哪些信息。

## Class 对象

在你想检查一个类的信息之前，你首先需要获取类的 Class 对象。Java 中的所有类型包括基本类型(int, long, float等等)，即使是数组都有与之关联的 Class 类的对象。如果你在编译期知道一个类的名字的话，那么你可以使用如下的方式获取一个类的 Class 对象。

```
Class myObjectClass = MyObject.class;
```

如果你在编译期不知道类的名字，但是你可以在运行期获得到类名的字符串,那么你则可以这么做来获取 Class 对象:

```
String className = ... ;//在运行期获取的类名字符串
Class class = Class.forName(className);
```

在使用 Class.forName() 方法时，你必须提供一个类的全名，这个全名包括类所在的包的名字。例如 MyObject 类位于 com.jenkov.myapp 包，那么他的全名就是 com.jenkov.myapp.MyObject。
如果在调用Class.forName()方法时，没有在编译路径下(classpath)找到对应的类，那么将会抛出ClassNotFoundException。

## 类名

你可以从 Class 对象中获取两个版本的类名。

通过 getName() 方法返回类的全限定类名（包含包名）：

```
Class aClass = ... //获取Class对象，具体方式可见Class对象小节
String className = aClass.getName();
```

如果你仅仅只是想获取类的名字(不包含包名)，那么你可以使用 getSimpleName()方法:

```
Class aClass = ... //获取Class对象，具体方式可见Class对象小节
String simpleClassName = aClass.getSimpleName();
```

## 修饰符

可以通过 Class 对象来访问一个类的修饰符， 即public,private,static 等等的关键字，你可以使用如下方法来获取类的修饰符：

```
Class  aClass = ... //获取Class对象，具体方式可见Class对象小节
int modifiers = aClass.getModifiers();
```

修饰符都被包装成一个int类型的数字，这样每个修饰符都是一个位标识(flag bit)，这个位标识可以设置和清除修饰符的类型。
可以使用 java.lang.reflect.Modifier 类中的方法来检查修饰符的类型：

```
Modifier.isAbstract(int modifiers);
Modifier.isFinal(int modifiers);
Modifier.isInterface(int modifiers);
Modifier.isNative(int modifiers);
Modifier.isPrivate(int modifiers);
Modifier.isProtected(int modifiers);
Modifier.isPublic(int modifiers);
Modifier.isStatic(int modifiers);
Modifier.isStrict(int modifiers);
Modifier.isSynchronized(int modifiers);
Modifier.isTransient(int modifiers);
Modifier.isVolatile(int modifiers);
```


## 包信息

可以使用 Class 对象通过如下的方式获取包信息：


```
Class  aClass = ... //获取Class对象，具体方式可见Class对象小节
Package package = aClass.getPackage();
```


通过 Package 对象你可以获取包的相关信息，比如包名，你也可以通过 Manifest 文件访问位于编译路径下 jar 包的指定信息，比如你可以在 Manifest 文件中指定包的版本编号。更多的 Package 类信息可以阅读 java.lang.Package。

## 父类

通过 Class 对象你可以访问类的父类，如下例：

```
Class superclass = aClass.getSuperclass();
```

可以看到 superclass 对象其实就是一个 Class 类的实例，所以你可以继续在这个对象上进行反射操作。

## 实现的接口  

可以通过如下方式获取指定类所实现的接口集合：

```
Class  aClass = ... //获取Class对象，具体方式可见Class对象小节
Class[] interfaces = aClass.getInterfaces();
```

由于一个类可以实现多个接口，因此 getInterfaces(); 方法返回一个 Class 数组，在 Java 中接口同样有对应的 Class 对象。
注意：getInterfaces() 方法仅仅只返回当前类所实现的接口。当前类的父类如果实现了接口，这些接口是不会在返回的 Class 集合中的，尽管实际上当前类其实已经实现了父类接口。

## 构造器

你可以通过如下方式访问一个类的构造方法：

```
Constructor[] constructors = aClass.getConstructors();
```

更多有关 Constructor 的信息可以访问 [Constructors](http://ifeve.com/java-reflection-constructors/)。

## 方法

你可以通过如下方式访问一个类的所有方法：

```
Method[] method = aClass.getMethods();
```

更多有关 Method 的信息可以访问 [Methods](http://ifeve.com/java-reflection-methods/)。

## 变量

你可以通过如下方式访问一个类的成员变量：

```
Field[] method = aClass.getFields();
```

更多有关 Field 的信息可以访问 [Fields](http://ifeve.com/java-reflection-fields/)。

## 注解

你可以通过如下方式访问一个类的注解：

```
Annotation[] annotations = aClass.getAnnotations();
```

更多有关 Annotation 的信息可以访问 [Annotations](http://ifeve.com/java-reflection-annotations/)。


本文链接地址: [Java Reflection(二)：Classes](http://ifeve.com/java-reflection-classes/)
