---
layout: post
title: '线程同步'
date: 2014-04-06 20:20
comments: true
categories: 
---
当多个线程访问同一个共享对象时，可能会出现数据不一致的现象。我们使用线程同步机制解决这种问题，以保证程序执行的一致性。

### 竞争条件

多线程编程中，竞争条件指可能导致共享数据不一致的情况。例如多个线程都试图更改共享数据的值，而更改操作不是原子的，这意味着可能某个线程会在更改操作进行到一半时被中断，这时其它进程读取到的数据就是不完整不一致的。为了防止这种情况的发生，我们使用锁来保证线程间的同步。

### 锁对象

java.util.concurrent 框架为加锁机制提供了基本的类。其中常用的一个是 ReentrantLock 类，使用它保护代码块的基本结构如下：

{% highlight java linenos %}
myLock.lock(); // a ReentrantLock object
try {
    critical section
} finally {
    myLock.unlock();
}
{% endhighlight %}

这一结构确保任何时刻只有一个线程进入临界区。一旦一个线程获得了锁对象，其它任何线程调用 lock 是都会被阻塞，直到第一个线程释放锁对象。

### 条件对象

有时线程获得了锁对象，进入临界区，却发现某一条件满足之后才能继续执行。这时就需要用条件对象来管理那些已经获得了一个锁但不能继续执行的线程。一个锁对象可以有多个条件对象。可以通过 newCondition 获得一个条件对象。一个线程调用的条件对象的 await 方法时，释放拥有的锁，并进入该条件的等待队列。直到另一个线程调用同一条件的 signalAll 方法时，原来的线程才被移出等待队列，变为可运行状态。使用条件对象的基本结构如下：

{% highlight java linenos %}
myLock.lock();
try {
    while (!(ok to proceed)) {
        condition.await();
    }
    
    do some task ...
    
    condition.signalAll();
} finally {
    myLock.unlock();
}
{% endhighlight %}

### synchronized 关键字

除了使用 Lock 和 Condition 进行锁控制，还有一种嵌入到 Java 语言内部的机制。 Java 中的每一个对象都有一个内部锁，如果一个方法声明为 synchronized，那么对象的内部锁将保护整个方法。也就是说，要调用该方法，线程必须获得对象的内部锁。下面两端代码可看作等价的：

{% highlight java linenos %}
public synchronized void method() {
    method body
}
{% endhighlight %}

{% highlight java linenos %}
public void method() {
    this.intrinsicLock.lock();
    try {
        method body
    } finally {
        this.intrinsicLock.unlock();
    }
}
{% endhighlight %}

内部对象锁只有一个相关条件。 Object.wait 方法添加一个线程到等待集中，notifyAll 方法解除等待线程的阻塞状态。将静态方法声明为 synchronized 也是合法的，调用该方法时使用方法所在类对应的类对象的内部锁。

### 同步代码块

除了调用同步方法，进入同步代码块也可以获得对象的内部锁。同步代码块有如下的形式，尝试进入该代码块会获得 obj 的锁：

{% highlight java linenos %}
synchronized (obj) {
    critical section
}
{% endhighlight %}