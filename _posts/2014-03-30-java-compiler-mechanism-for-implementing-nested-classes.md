---
layout: post
title: 'Java 编译器实现嵌套类的机制'
---
Java 允许将一个类定义在另一个类之中，这样的类称为嵌套类（nested class），包含嵌套类的类称为外部类（outer class）。其中，嵌套类又可进一步分为静态内部类（static nested class）、内部类（inner class）和局部类（local class）。嵌套类同泛型一样，只是一种编译期的机制，JVM 并不区分嵌套类和普通的类，因此为了进一步了解嵌套类，需要知道编译器都做了哪些事。

### 内部类被编译为常规类文件

```java
class OuterClass {
    class InnerClass {}
}
```

这个程序编译后会产生两个类文件，分别是 OuterClass.class 和 OuterClass$InnerClass.class，对虚拟机而言，这两个类均为普通类文件。但和普通类不同的是，编译器会自动在内部类中添加一个外部类对象的引用，可以通过 `javap -p ClassName` 查看编译后的类文件，这时会看到下面的结果：

```
class OuterClass$InnerClass {
    final OuterClass this$0;
    OuterClass$InnerClass(OuterClass);
}
```

为了能在内部类中访问外部类，编译器添加了一个实例域 this$0 以及包含一个参数的构造器。在内部类中，可以通过 `OuterClass.this` 获得外部类对象的引用。

### 内部类访问外部类的实例域

我们已经知道内部类包含了外部类对象的引用，因此可以访问外部类的公有成员，但内部类是如何访问外部类的私有成员的呢？先看一下如下代码及其反编译的结果：

```java
class OuterClass {
    private int foo;
    
    class InnerClass {
        public void fun() {
            System.out.println(foo);
        }
    }
}
```

```
// javap -p OuterClass
class OuterClass {
  private int foo;
  OuterClass();
  static int access$000(OuterClass);
}
```

可以发现编译器为外部类添加了一个静态方法 access$000，通过该方法和外部类对象的引用便可以在内部类中访问外部类的私有成员。

### 局部类访问局部变量

局部类中可以访问声明为 final 的局部变量，这看起来似乎是很自然的，但看看下面的代码就会发现问题没有那么简单：

```java
class OuterClass {
	public void fun(final int foo) {
		class LocalClass implements Runnable {
			public void run() {
				while (true) {
					try {
						Thread.sleep(1000);	
					} catch (InterruptedException e) {
						e.printStackTrace();
					}
					
					System.out.println(foo);
				}
			}
		}

		new Thread(new LocalClass()).start();
	}

	public static void main(String[] args) {
		new OuterClass().fun(42);
	}
}
```

查看一下这个程序的执行过程可以发现，当调用 fun 方法时，创建了一个新的线程，该线程中的 LocalClass 不停地打印出局部变量 foo，但是开启了新线程之后，fun 方法结束，局部变量 foo 也就不存在了，那 LocalClass 是如何打印出该局部变量的呢？看一下反编译的结果：

```
// javap -p OuterClass$1LocalClass
class OuterClass$1LocalClass implements java.lang.Runnable {
  final int val$foo;
  final OuterClass this$0;
  OuterClass$1LocalClass();
  public void run();
}
```

可以发现局部类中保存了局部变量的一个副本，通过泛型查看局部类的构造器可以得到如下结果：

```java
import java.lang.reflect.*;

public class ClassChecker {
    public static void main(String[] args) throws Exception {
        String className = "OuterClass$1LocalClass";
        Class<?> cl = Class.forName(className);
        for (Constructor cons : cl.getDeclaredConstructors()) {
            System.out.println(cons);
        }
    }
}
```
```
// Output from above program
OuterClass$1LocalClass(OuterClass,int)
```

可以发现编译器为局部类生成的构造器中包含了一个 int 类型的参数，通过这个参数可以在局部类中保存一个局部变量的副本。