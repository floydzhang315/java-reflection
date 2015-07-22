#教程

[原文地址](http://tutorials.jenkov.com/java-reflection/dynamic-class-loading-reloading.html)  作者: Jakob Jenkov 译者:叶文海（yewenhai@gmail.com）校对：方腾飞

Java 反射机制可以让我们在编译期(Compile Time)之外的运行期(Runtime)检查类，接口，变量以及方法的信息。反射还可以让我们在运行期实例化对象，调用方法，通过调用 get/set 方法获取变量的值。

Java 反射机制功能强大而且非常实用。举个例子，你可以用反射机制把Java对象映射到数据库表，就像 Butterfly Persistence（译者注：原作者所编写的框架）所做的那样，或者把脚本中的一段语句在运行期映射到相应的对象调用方法上，就像 Butterfly Container（译者注：原作者所编写的框架）在解析它的配置脚本时所做的那样。


目前在互联网上已经有不胜枚举的 Java 反射指南，然而大多数的指南包括 Sun 公司所发布的反射指南中都仅仅只是介绍了一些反射的表面内容以及它的潜能。

在这个系列的文章中，我们会比其他指南更深入的去理解 Java 反射机制，它会阐述 Java 反射机制的基本原理包括如何去使用数组，注解，泛型以及动态代理还有类的动态加载以及类的重载的实现。同时也会向你展示如何实现一些比较有特性的功能，比如从一个类中读取所有的 get/set 方法，或者访问一个类的私有变量以及私有方法。在这个系列的指南中同时也会说明一些非反射相关的但是令人困惑的问题，比如哪些泛型信息在运行时是有效的，一些人声称所有的泛型信息在运行期都会消失，其实这是不对的。

原创文章，转载请注明： 转载自[并发编程网 – ifeve.com](http://ifeve.com/)

本文链接地址: [Java Reflection教程](http://ifeve.com/java-reflection/)