# Java 动态代理

[原文地址](http://tutorials.jenkov.com/java-reflection/dynamic-proxies.html) 

作者: Jakob Jenkov 译者:叶文海（yewenhai@gmail.com）

## 内容索引

1. 创建代理
2. InvocationHandler 接口

## 常见用例

1. 数据库连接以及事物管理
2. 单元测试中的动态 Mock 对象
3. 自定义工厂与依赖注入（DI）容器之间的适配器
4. 类似 AOP 的方法拦截器

利用Java反射机制你可以在运行期动态的创建接口的实现。 java.lang.reflect.Proxy 类就可以实现这一功能。这个类的名字（译者注：Proxy 意思为代理）就是为什么把动态接口实现叫做动态代理。动态的代理的用途十分广泛，比如数据库连接和事物管理（transaction management）还有单元测试时用到的动态 mock 对象以及 AOP 中的方法拦截功能等等都使用到了动态代理。


## 创建代理

你可以通过使用 Proxy.newProxyInstance()方法创建动态代理。 newProxyInstance()方法有三个参数：
1、类加载器（ClassLoader）用来加载动态代理类。
2、一个要实现的接口的数组。
3、一个 InvocationHandler 把所有方法的调用都转到代理上。
如下例：

```
InvocationHandler handler = new MyInvocationHandler();
MyInterface proxy = (MyInterface) Proxy.newProxyInstance(
                            MyInterface.class.getClassLoader(),
                            new Class[] { MyInterface.class },
                            handler);
```

在执行完这段代码之后，变量 proxy 包含一个 MyInterface 接口的的动态实现。所有对 proxy 的调用都被转向到实现了 InvocationHandler 接口的 handler 上。有关 InvocationHandler 的内容会在下一段介绍。

## InvocationHandler 接口

在前面提到了当你调用 Proxy.newProxyInstance()方法时，你必须要传入一个 InvocationHandler 接口的实现。所有对动态代理对象的方法调用都会被转向到 InvocationHandler 接口的实现上，下面是 InvocationHandler 接口的定义：

```
public interface InvocationHandler{
  Object invoke(Object proxy, Method method, Object[] args)
         throws Throwable;
}
```

下面是它的实现类的定义：

```
public class MyInvocationHandler implements InvocationHandler{

  public Object invoke(Object proxy, Method method, Object[] args)
  throws Throwable {
    //do something "dynamic"
  }
}
```

传入 invoke()方法中的 proxy 参数是实现要代理接口的动态代理对象。通常你是不需要他的。

invoke()方法中的 Method 对象参数代表了被动态代理的接口中要调用的方法，从这个 method 对象中你可以获取到这个方法名字，方法的参数，参数类型等等信息。关于这部分内容可以查阅之前有关 Method 的文章。

Object 数组参数包含了被动态代理的方法需要的方法参数。注意：原生数据类型（如int，long等等）方法参数传入等价的包装对象（如Integer， Long等等）。


## 常见用例

动态代理常被应用到以下几种情况中

* 数据库连接以及事物管理
* 单元测试中的动态 Mock 对象
* 自定义工厂与依赖注入（DI）容器之间的适配器
* 类似 AOP 的方法拦截器

## 数据库连接以及事物管理

Spring 框架中有一个事物代理可以让你提交/回滚一个事物。它的具体原理在 [Advanced Connection and Transaction Demarcation and Propagation](http://tutorials.jenkov.com/java-persistence/advanced-connection-and-transaction-demarcation-and-propagation.html) 一文中有详细描述，所以在这里我就简短的描述一下，方法调用序列如下：

```
web controller --> proxy.execute(...);
  proxy --> connection.setAutoCommit(false);
  proxy --> realAction.execute();
    realAction does database work
  proxy --> connection.commit();
```

## 单元测试中的动态 Mock 对象

[Butterfly Testing](http://butterfly.jenkov.com/)工具通过动态代理来动态实现桩（stub），mock 和代理类来进行单元测试。在测试类A的时候如果用到了接口 B，你可以传给 A 一个实现了 B 接口的 mock 来代替实际的 B 接口实现。所有对接口B的方法调用都会被记录，你可以自己来设置 B 的 mock 中方法的返回值。
而且 Butterfly Testing 工具可以让你在 B 的 mock 中包装真实的 B 接口实现，这样所有调用 mock 的方法都会被记录，然后把调用转向到真实的 B 接口实现。这样你就可以检查B中方法真实功能的调用情况。例如：你在测试 DAO 时你可以把真实的数据库连接包装到 mock 中。这样的话就与真实的情况一样，DAO 可以在数据库中读写数据，mock 会把对数据库的读写操作指令都传给数据库，你可以通过 mock 来检查 DAO 是不是以正确的方式来使用数据库连接，比如你可以检查是否调用了 connection.close()方法。这种情况是不能简单的依靠调用 DAO 方法的返回值来判断的。

## 自定义工厂与依赖注入（DI）容器之间的适配器

依赖注入容器 [Butterfly Container](http://butterfly.jenkov.com/) 有一个非常强大的特性可以让你把整个容器注入到这个容器生成的 bean 中。但是，如果你不想依赖这个容器的接口，这个容器可以适配你自己定义的工厂接口。你仅仅需要这个接口而不是接口的实现，这样这个工厂接口和你的类看起来就像这样：

```
public interface IMyFactory {
  Bean   bean1();
  Person person();
  ...
}
 

public class MyAction{

  protected IMyFactory myFactory= null;

  public MyAction(IMyFactory factory){
    this.myFactory = factory;
  }

  public void execute(){
    Bean bean = this.myFactory.bean();
    Person person = this.myFactory.person();
  }

}
```

当 MyAction 类调用通过容器注入到构造方法中的 IMyFactory 实例的方法时，这个方法调用实际先调用了 IContainer.instance()方法，这个方法可以让你从容器中获取实例。这样这个对象可以把 Butterfly Container 容器在运行期当成一个工厂使用，比起在创建这个类的时候进行注入，这种方式显然更好。而且这种方法没有依赖到 Butterfly Container 中的任何接口。

## 类似 AOP 的方法拦截器

Spring 框架可以拦截指定 bean 的方法调用，你只需提供这个 bean 继承的接口。Spring 使用动态代理来包装 bean。所有对 bean 中方法的调用都会被代理拦截。代理可以判断在调用实际方法之前是否需要调用其他方法或者调用其他对象的方法，还可以在 bean 的方法调用完毕之后再调用其他的代理方法。

本文链接地址: [Java Reflection(十一):动态代理](http://ifeve.com/java-reflection-11-dynamic-proxies/)