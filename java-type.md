# Java 泛型

[原文地址](http://tutorials.jenkov.com/java-reflection/generics.html) 作者: Jakob Jenkov 译者:叶文海（yewenhai@gmail.com）

## 内容索引

1. 运用泛型反射的经验法则
2. 泛型方法返回类型
3. 泛型方法参数类型
4. 泛型变量类型

我常常在一些文章以及论坛中读到说 Java 泛型信息在编译期被擦除（erased）所以你无法在运行期获得有关泛型的信息。其实这种说法并不完全正确的，在一些情况下是可以在运行期获取到泛型的信息。这些情况其实覆盖了一些我们需要泛型信息的需求。在本节中我们会演示一下这些情况。



## 运用泛型反射的经验法则

下面是两个典型的使用泛型的场景：
1、声明一个需要被参数化（parameterizable）的类/接口。
2、使用一个参数化类。

当你声明一个类或者接口的时候你可以指明这个类或接口可以被参数化， java.util.List 接口就是典型的例子。你可以运用泛型机制创建一个标明存储的是 String 类型 list，这样比你创建一个 Object 的l ist 要更好。

当你想在运行期参数化类型本身，比如你想检查 java.util.List 类的参数化类型，你是没有办法能知道他具体的参数化类型是什么。这样一来这个类型就可以是一个应用中所有的类型。但是，当你检查一个使用了被参数化的类型的变量或者方法，你可以获得这个被参数化类型的具体参数。总之：

你不能在运行期获知一个被参数化的类型的具体参数类型是什么，但是你可以在用到这个被参数化类型的方法以及变量中找到他们，换句话说就是获知他们具体的参数化类型。
在下面的段落中会向你演示这类情况。


## 泛型方法返回类型

如果你获得了 java.lang.reflect.Method 对象，那么你就可以获取到这个方法的泛型返回类型信息。如果方法是在一个被参数化类型之中（译者注：如 T fun()）那么你无法获取他的具体类型，但是如果方法返回一个泛型类（译者注：如 List fun()）那么你就可以获得这个泛型类的具体参数化类型。你可以在“[Java Reflection: Methods](http://tutorials.jenkov.com/java-reflection/generics.html)”中阅读到有关如何获取Method对象的相关内容。下面这个例子定义了一个类这个类中的方法返回类型是一个泛型类型：

```
  public class MyClass {

  protected List<String> stringList = ...;

  public List<String> getStringList(){
    return this.stringList;
  }
}
```

我们可以获取 getStringList()方法的泛型返回类型，换句话说，我们可以检测到 getStringList()方法返回的是 List 而不仅仅只是一个 List。如下例：

```
Method method = MyClass.class.getMethod("getStringList", null);

Type returnType = method.getGenericReturnType();

if(returnType instanceof ParameterizedType){
    ParameterizedType type = (ParameterizedType) returnType;
    Type[] typeArguments = type.getActualTypeArguments();
    for(Type typeArgument : typeArguments){
        Class typeArgClass = (Class) typeArgument;
        System.out.println("typeArgClass = " + typeArgClass);
    }
}
```


这段代码会打印出 “typeArgClass = java.lang.String”，Type[]数组typeArguments 只有一个结果 – 一个代表 java.lang.String 的 Class 类的实例。Class 类实现了 Type 接口。


## 泛型方法参数类型

你同样可以通过反射来获取方法参数的泛型类型，下面这个例子定义了一个类，这个类中的方法的参数是一个被参数化的 List：

```
public class MyClass {
  protected List<String> stringList = ...;

  public void setStringList(List<String> list){
    this.stringList = list;
  }
}
```

你可以像这样来获取方法的泛型参数：

```
method = Myclass.class.getMethod("setStringList", List.class);

Type[] genericParameterTypes = method.getGenericParameterTypes();

for(Type genericParameterType : genericParameterTypes){
    if(genericParameterType instanceof ParameterizedType){
        ParameterizedType aType = (ParameterizedType) genericParameterType;
        Type[] parameterArgTypes = aType.getActualTypeArguments();
        for(Type parameterArgType : parameterArgTypes){
            Class parameterArgClass = (Class) parameterArgType;
            System.out.println("parameterArgClass = " + parameterArgClass);
        }
    }
}
```

这段代码会打印出”parameterArgType = java.lang.String”。Type[]数组 parameterArgTypes 只有一个结果 – 一个代表 java.lang.String 的 Class 类的实例。Class 类实现了Type接口。

## 泛型变量类型

同样可以通过反射来访问公有（Public）变量的泛型类型，无论这个变量是一个类的静态成员变量或是实例成员变量。你可以在“[Java Reflection: Fields](http://tutorials.jenkov.com/java-reflection/fields.html)”中阅读到有关如何获取 Field 对象的相关内容。这是之前的一个例子，一个定义了一个名为 stringList 的成员变量的类。

```
public class MyClass {
  public List<String> stringList = ...;
}
 

Field field = MyClass.class.getField("stringList");

Type genericFieldType = field.getGenericType();

if(genericFieldType instanceof ParameterizedType){
    ParameterizedType aType = (ParameterizedType) genericFieldType;
    Type[] fieldArgTypes = aType.getActualTypeArguments();
    for(Type fieldArgType : fieldArgTypes){
        Class fieldArgClass = (Class) fieldArgType;
        System.out.println("fieldArgClass = " + fieldArgClass);
    }
}
```

这段代码会打印出”fieldArgClass = java.lang.String”。Type[]数组 fieldArgClass 只有一个结果 – 一个代表 java.lang.String 的 Class 类的实例。Class 类实现了 Type 接口。

原创文章，转载请注明： 转载自[并发编程网 – ifeve.com](http://ifeve.com/)

本文链接地址: [Java Reflection(九):泛型](http://ifeve.com/java-reflection-9-generics/)