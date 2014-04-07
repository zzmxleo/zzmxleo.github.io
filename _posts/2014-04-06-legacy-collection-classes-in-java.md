---
layout: post
title: '[黑马程序员]Java 中遗留的集合类'
---
除了之前介绍的 Java 集合框架中的类，Java 类库中还有很多遗留的集合类，这些类大多是在集合框架出现之前就已经存在的。虽然已经被集合框架的新类替代，但在很多地方还需要直接操作这些遗留类，因此本文就来总结一下 Java 中遗留的集合类。

### 遗留的集合类并入集合框架

下图总结了遗留的集合类在集合框架中的位置。

![old-classes.png](http://user-image.logdown.io/user/3258/blog/3302/post/192599/DJYHpsPpSEmJIJJYwysl_old-classes.png)

### Vector

Vector 对应集合框架中的 ArrayList，因为 Vector 同样实现了 List 和 RandomAccess 接口，因此可以用操作 ArrayList 的方法操作 Vector。不同的是 Vector 是线程安全的，而 ArrayList 则没有保证线程安全。除了使用 Iterator，还可以使用 elements() 获得 Enumeration 来对 Vector 进行遍历，Enumeration 与集合框架中的 Iterator 相对应。

### Stack

Stack 扩展了 Vector，提供了模拟 LIFO stack 的方法：push(e), pop(), peek(). 通常建议使用 Deque 接口的实现类来实现 LIFO stack，例如：

{% highlight java linenos %}
Deque<Integer> stack = new ArrayDeque<Integer>();
{% endhighlight %}

### Hashable

Hashtable 对应集合框架中的 HashMap，因为 Hashtable 实现了 Map 接口，所以可以像使用 HashMap 那样使用 Hashtable。不同的是 Hashtable 是线程安全的，而 HashMap 是线程不安全的（标准库中提供了线程安全的 ConcurrentHashMap，建议在要求同步时使用它而不是 Hashtable）。

Hashtable 中提供了对键和值遍历的方法：elements(), keys()。这两个方法返回 Enumeration 对象，可以用来遍历键或值的集合。

### Properties

Properties 表示属性集，通常用来存储配置信息。Properties 是特殊的 Hashtable，它的键和值均要求是字符串，因此建议使用 Properties 中的 getProperty 和 setProperty 方法操作 Properties，保证属性集中的键值均为字符串类型。可以使用 StringPropertyNames() 对属性集中的键值进行遍历。此外，Properties 中还使用一个二级表存放默认值。

Properties 允许直接和输入输出流进行交互。store 方法将属性集输出到输出流中，load 方法从输入流中载入属性集。下面的方法分别存储和加载了属性集：

{% highlight java linenos %}
public static void storeProperties(Properties prop, File file, String comment) throws IOException {
    try (OutputStream out = new FileOutputStream(file)) {
        prop.store(out, comment);
    }
}
{% endhighlight %}

{% highlight java linenos %}
public static void loadProperties(Properties prop, File file) throws IOException {
    try (InputStream in = new FileInputStream(file)) {
        prop.load(in);
    }
}
{% endhighlight %}

还可以通过 java.lang.System 类中的静态方法获得和设置系统属性，例如 System.getProperties() 方法返回系统属性集，System.getProperty(String key) 返回指定键值对应的属性值，System.setProperty(String key, String value) 设置相应的属性值。