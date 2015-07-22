# Java 访问器

[原文地址](http://tutorials.jenkov.com/java-reflection/getters-setters.html) 

作者: Jakob Jenkov 译者:叶文海（yewenhai@gamil.com）

使用 Java 反射你可以在运行期检查一个方法的信息以及在运行期调用这个方法，使用这个功能同样可以获取指定类的 getters 和 setters，你不能直接寻找 getters 和 setters，你需要检查一个类所有的方法来判断哪个方法是 getters 和 setters。


首先让我们来规定一下 getters 和 setters 的特性：

## Getter

Getter 方法的名字以 get 开头，没有方法参数，返回一个值。

## Setter

Setter 方法的名字以 set 开头，有一个方法参数。

setters 方法有可能会有返回值也有可能没有，一些 Setter 方法返回 void，一些用来设置值，有一些对象的 setter 方法在方法链中被调用（译者注：这类的 setter 方法必须要有返回值），因此你不应该妄自假设 setter 方法的返回值，一切应该视情况而定。

下面是一个获取 getter方法和 setter方法的例子：

查看源代码打印帮助

```
</pre>
<pre class="codeBox">public static void printGettersSetters(Class aClass){
  Method[] methods = aClass.getMethods();

  for(Method method : methods){
    if(isGetter(method)) System.out.println("getter: " + method);
    if(isSetter(method)) System.out.println("setter: " + method);
  }
}

public static boolean isGetter(Method method){
  if(!method.getName().startsWith("get"))      return false;
  if(method.getParameterTypes().length != 0)   return false;
  if(void.class.equals(method.getReturnType()) return false;
  return true;
}

public static boolean isSetter(Method method){
  if(!method.getName().startsWith("set")) return false;
  if(method.getParameterTypes().length != 1) return false;
  return true;
}</pre>
<pre>
```

本文链接地址: [Java Reflection(六):Getters and Setters](http://ifeve.com/java-reflection%E5%85%ADgetters-and-setters/)