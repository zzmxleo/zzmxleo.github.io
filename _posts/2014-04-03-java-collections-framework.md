---
layout: post
title: 'Java 集合框架'
---
在程序中经常会有对大量对象进行操作的需求，Java 类库的集合框架提供了满足这种需求的常用数据结构及算法。下面先从宏观上总结一下 Java 集合框架的结构以及重要的类和接口。

### 集合框架的接口

![interfaces-collection-frame.jpg](http://user-image.logdown.io/user/3258/blog/3302/post/192358/U1fq37tiR8mTPNuLBI40_interfaces-collection-frame.jpg)

集合框架的两个基本接口是 Collection 和 Map，分别定义了集合和表，集合又衍生为集、有序集和队列。Collection 继承了 Iterable 接口，因此集合类都可以使用迭代器进行遍历。Iterator 接口定义了迭代器。RandomAccess 是一个标记接口，标记支持高效随机访问的类，比如 ArrayList。

### Collection 接口

Collection 是集合框架的根接口，表示一组对象，这些对象称为集合的元素。Collection 没有直接的实现类，而是衍生出多个更具体的子接口，其中最常见的便是 Set，List，和 Queue. 很多与集合有关的算法会要求 Collection 类型的参数以实现算法的通用性。

Java 标准库中约定 Collection 的实现类至少提供两个构造器：一个是空参构造器，创建一个空集合；另一个要求一个 Collection 类型的参数，创建一个包含参数集合所有元素的特定类型的实例。通常可以使用第二个构造器实现不同类型集合的转换。

一些 Collection 的实现类对其元素有某些限制，比如是否允许 null 值，要求元素实现特定的接口等。

Collection 接口没有要求集合是线程安全的，同步性由具体的实现类决定。

### Iterator 接口

通过 Iterator 可以实现对集合的遍历，它替代了旧集合框架中的 Enumeration 接口，改进了方法的命名并且允许调用者删除集合中的元素。

### Set 接口

Set 不允许包含重复的元素，是对数学集的一个抽象。Set 中的批量操作方法 addAll, removeAll, retainAll 分别模拟了数学上的并、差和交运算。

Set 规定 equals 方法返回 true 当且仅当比较对象也是 Set，大小相同且含有相同的元素。hashCode 方法定义为各元素的 hashCode 之和

### SortedSet 接口

SortedSet 用元素的自然顺序（natural ordering）或比较器（Comparator）对集合中的元素进行排序，它的迭代器将按从小到大的顺序遍历元素。为了实现排序，集合中的元素必须实现 Comparable 接口或者在构造集合时提供 Comparator 比较器。

SortedSet 约定它的实现类需要提供四个构造器：1）空参构造器 2）接收一个 Comparator 类型参数的构造器 3）接收一个 Collection 类型参数的构造器 4）接收一个 SortedSet 类型参数的构造器。

SortedSet 中的 comparator 方法返回该 SortedSet 使用的比较器，若使用自然顺序进行排序，则返回 null.

SortedSet 中定义了三个视图方法，它们返回位于特定区间的子视图，且这些区间都是左闭右开的。

### NavigableSet 接口

NavigableSet 扩展了 SortedSet，定义了一些搜索匹配元素的方法，并支持以升序或降序对集合元素进行遍历。

### List 接口

List 代表有序集，用户可以控制每一个元素在集合中的位置，因此可以通过随机访问方法操作 List 中的元素。虽然 List 支持随机访问，但并没有保证它的执行效率，所以通常只对实现了 RandomAccess 接口的 List 类才使用随机访问方法（例如 ArrayList）。

List 提供了一种特殊的迭代器 ListIterator， 它允许用户插入和修改元素，并且允许反向迭代。

List 还提供了一个视图方法 subList， 它返回一个子视图，并支持在这个子视图上做任何操作。

### Queue 接口

Queue 表示队列，用于存储待操作的元素（比如阻塞队列 BlockingQueue 中存储待执行的任务）。Queue 提供了用于添加、删除和查看元素的方法，这些方法分为两类：一类在操作失败时抛出异常，一类则返回一个特殊值（例如 null 或 false）。下表总结了这些方法：

|         | Throws exception | Returns special value |
|:------- |:---------------- |:--------------------- |
| Insert  | add(e)           | offer(e)              |
| Remove  | remove()         | poll()                |
| Examine | element()        | peek()                |

最常见的三种 Queue 是 FIFO queue, LIFO queue, Priority queue, 它们根据不同的规则对元素进行了排列。

因为 null 是 poll 方法的一个特殊返回值，所以建议不要向 Queue 中添加 null 值。

### Deque 接口

Deque 接口扩展了 Queue，支持在队列的两端进行各种操作。经常用来模拟 FIFO queue 和 LIFO stack。

### Map 接口

Map 表示映射表，可以看做键值对的集合，其中键值是唯一的。Map 提供了三种集合视图，可用来查看键的集合、值的集合和键值对的集合。Map 的子接口 SortedMap 和 NavigableMap 与 SortedSet 和 NavigableSet 类似。