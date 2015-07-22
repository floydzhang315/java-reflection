# Java 注解

[原文地址](http://tutorials.jenkov.com/java-reflection/annotations.html)

 作者: Jakob Jenkov 译者:叶文海（yewenhai@gmail.com）

## 内容索引

1. 什么是注解
2. 类注解
3. 方法注解
4. 参数注解
5. 变量注解

利用 Java 反射机制可以在运行期获取 Java 类的注解信息。


## 什么是注解

注解是 Java 5 的一个新特性。注解是插入你代码中的一种注释或者说是一种元数据（meta data）。这些注解信息可以在编译期使用预编译工具进行处理（pre-compiler tools），也可以在运行期使用 Java 反射机制进行处理。下面是一个类注解的例子：


```
@MyAnnotation(name="someName",  value = "Hello World")
public class TheClass {
}
```

在 TheClass 类定义的上面有一个@MyAnnotation 的注解。注解的定义与接口的定义相似，下面是MyAnnotation注解的定义：

```
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.TYPE)

public @interface MyAnnotation {
  public String name();
  public String value();
}
```


在 interface 前面的@符号表名这是一个注解，一旦你定义了一个注解之后你就可以将其应用到你的代码中，就像之前我们的那个例子那样。
在注解定义中的两个指示@Retention(RetentionPolicy.RUNTIME)和@Target(ElementType.TYPE)，说明了这个注解该如何使用。
@Retention(RetentionPolicy.RUNTIME)表示这个注解可以在运行期通过反射访问。如果你没有在注解定义的时候使用这个指示那么这个注解的信息不会保留到运行期，这样反射就无法获取它的信息。
@Target(ElementType.TYPE) 表示这个注解只能用在类型上面（比如类跟接口）。你同样可以把Type改为Field或者Method，或者你可以不用这个指示，这样的话你的注解在类，方法和变量上就都可以使用了。
关于 Java 注解更详细的讲解可以访问 [Java Annotations tutorial](http://tutorials.jenkov.com/java/annotations.html)。


## 类注解

你可以在运行期访问类，方法或者变量的注解信息，下是一个访问类注解的例子：


```
 Class aClass = TheClass.class;
Annotation[] annotations = aClass.getAnnotations();

for(Annotation annotation : annotations){
    if(annotation instanceof MyAnnotation){
        MyAnnotation myAnnotation = (MyAnnotation) annotation;
        System.out.println("name: " + myAnnotation.name());
        System.out.println("value: " + myAnnotation.value());
    }
}
```

你还可以像下面这样指定访问一个类的注解：

```
Class aClass = TheClass.class;
Annotation annotation = aClass.getAnnotation(MyAnnotation.class);

if(annotation instanceof MyAnnotation){
    MyAnnotation myAnnotation = (MyAnnotation) annotation;
    System.out.println("name: " + myAnnotation.name());
    System.out.println("value: " + myAnnotation.value());
}
```

## 方法注解

下面是一个方法注解的例子：

```
public class TheClass {
  @MyAnnotation(name="someName",  value = "Hello World")
  public void doSomething(){}
}
```

你可以像这样访问方法注解：

```
Method method = ... //获取方法对象
Annotation[] annotations = method.getDeclaredAnnotations();

for(Annotation annotation : annotations){
    if(annotation instanceof MyAnnotation){
        MyAnnotation myAnnotation = (MyAnnotation) annotation;
        System.out.println("name: " + myAnnotation.name());
        System.out.println("value: " + myAnnotation.value());
    }
}
```


你可以像这样访问指定的方法注解：

```
Method method = ... // 获取方法对象
Annotation annotation = method.getAnnotation(MyAnnotation.class);

if(annotation instanceof MyAnnotation){
    MyAnnotation myAnnotation = (MyAnnotation) annotation;
    System.out.println("name: " + myAnnotation.name());
    System.out.println("value: " + myAnnotation.value());
}
```


## 参数注解

方法参数也可以添加注解，就像下面这样：

```
public class TheClass {
  public static void doSomethingElse(
        @MyAnnotation(name="aName", value="aValue") String parameter){
  }
}
```

你可以通过 Method对象来访问方法参数注解：

```
Method method = ... //获取方法对象
Annotation[][] parameterAnnotations = method.getParameterAnnotations();
Class[] parameterTypes = method.getParameterTypes();

int i=0;
for(Annotation[] annotations : parameterAnnotations){
  Class parameterType = parameterTypes[i++];

  for(Annotation annotation : annotations){
    if(annotation instanceof MyAnnotation){
        MyAnnotation myAnnotation = (MyAnnotation) annotation;
        System.out.println("param: " + parameterType.getName());
        System.out.println("name : " + myAnnotation.name());
        System.out.println("value: " + myAnnotation.value());
    }
  }
}
```


需要注意的是 Method.getParameterAnnotations()方法返回一个注解类型的二维数组，每一个方法的参数包含一个注解数组。


## 变量注解

下面是一个变量注解的例子：

```
public class TheClass {

  @MyAnnotation(name="someName",  value = "Hello World")
  public String myField = null;
}
```


你可以像这样来访问变量的注解：


```
Field field = ... //获取方法对象</pre>
<pre>Annotation[] annotations = field.getDeclaredAnnotations();

for(Annotation annotation : annotations){
 if(annotation instanceof MyAnnotation){
 MyAnnotation myAnnotation = (MyAnnotation) annotation;
 System.out.println("name: " + myAnnotation.name());
 System.out.println("value: " + myAnnotation.value());
 }
}
```


你可以像这样访问指定的变量注解：

```
Field field = ...//获取方法对象</pre>
<pre>
Annotation annotation = field.getAnnotation(MyAnnotation.class);

if(annotation instanceof MyAnnotation){
 MyAnnotation myAnnotation = (MyAnnotation) annotation;
 System.out.println("name: " + myAnnotation.name());
 System.out.println("value: " + myAnnotation.value());
}
```

本文链接地址: [Java Reflection(八):注解](http://ifeve.com/java-reflection-8-annotation/)