# Java 私有变量和私有方法

[原文地址](http://tutorials.jenkov.com/java-reflection/private-fields-and-methods.html)    

作者: Jakob Jenkov 译者:叶文海（yewenhai@gamil.com）

## 内容索引

1. 访问私有变量
2. 访问私有方法

在通常的观点中从对象的外部访问私有变量以及方法是不允许的，但是 Java 反射机制可以做到这一点。使用这个功能并不困难，在进行单元测试时这个功能非常有效。本节会向你展示如何使用这个功能。

注意：这个功能只有在代码运行在单机 Java 应用(standalone Java application)中才会有效,就像你做单元测试或者一些常规的应用程序一样。如果你在 Java Applet 中使用这个功能，那么你就要想办法去应付 SecurityManager 对你限制了。但是一般情况下我们是不会这么做的，所以在本节里面我们不会探讨这个问题。


## 访问私有变量

要想获取私有变量你可以调用 Class.getDeclaredField(String name)方法或者 Class.getDeclaredFields()方法。   

Class.getField(String name)和 Class.getFields()只会返回公有的变量，无法获取私有变量。下面例子定义了一个包含私有变量的类，在它下面是如何通过反射获取私有变量的例子：

```
public class PrivateObject {

  private String privateString = null;

  public PrivateObject(String privateString) {
    this.privateString = privateString;
  }
}
```

```
PrivateObject privateObject = new PrivateObject("The Private Value");

Field privateStringField = PrivateObject.class.
            getDeclaredField("privateString");

privateStringField.setAccessible(true);

String fieldValue = (String) privateStringField.get(privateObject);
System.out.println("fieldValue = " + fieldValue);
```


这个例子会输出”fieldValue = The Private Value”，The Private  Value 是 PrivateObject 实例的 privateString 私有变量的值，注意调用 PrivateObject.class.getDeclaredField(“privateString”)方法会返回一个私有变量，这个方法返回的变量是定义在 PrivateObject 类中的而不是在它的父类中定义的变量。
注意 privateStringField.setAccessible(true)这行代码，通过调用  setAccessible()方法会关闭指定类 Field 实例的反射访问检查，这行代码执行之后不论是私有的、受保护的以及包访问的作用域，你都可以在任何地方访问，即使你不在他的访问权限作用域之内。但是你如果你用一般代码来访问这些不在你权限作用域之内的代码依然是不可以的，在编译的时候就会报错。


## 访问私有方法

访问一个私有方法你需要调用 Class.getDeclaredMethod(String name, Class[] parameterTypes)或者 Class.getDeclaredMethods() 方法。 Class.getMethod(String name, Class[] parameterTypes)和 Class.getMethods()方法，只会返回公有的方法，无法获取私有方法。下面例子定义了一个包含私有方法的类，在它下面是如何通过反射获取私有方法的例子：


```
public class PrivateObject {

  private String privateString = null;

  public PrivateObject(String privateString) {
    this.privateString = privateString;
  }

  private String getPrivateString(){
    return this.privateString;
  }
}
```

```
PrivateObject privateObject = new PrivateObject("The Private Value");

Method privateStringMethod = PrivateObject.class.
        getDeclaredMethod("getPrivateString", null);

privateStringMethod.setAccessible(true);

String returnValue = (String)
        privateStringMethod.invoke(privateObject, null);

System.out.println("returnValue = " + returnValue);
```

这个例子会输出”returnValue = The Private Value”，The Private Value 是 PrivateObject 实例的 getPrivateString()方法的返回值。
PrivateObject.class.getDeclaredMethod(“privateString”)方法会返回一个私有方法，这个方法是定义在 PrivateObject 类中的而不是在它的父类中定义的。
同样的，注意 Method.setAcessible(true)这行代码，通过调用 setAccessible()方法会关闭指定类的 Method 实例的反射访问检查，这行代码执行之后不论是私有的、受保护的以及包访问的作用域，你都可以在任何地方访问，即使你不在他的访问权限作用域之内。但是你如果你用一般代码来访问这些不在你权限作用域之内的代码依然是不可以的，在编译的时候就会报错。


本文链接地址: [Java Reflection(七):私有变量和私有方法](http://ifeve.com/java-reflection-7/)

