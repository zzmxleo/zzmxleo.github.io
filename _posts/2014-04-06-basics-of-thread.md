---
layout: post
title: '[黑马程序员]线程的基本操作'
date: 2014-04-06 16:01
comments: true
categories: [java]
---
我们通常需要在一个程序中同时执行多件任务，Java 的多线程机制满足了这个需求。

### 线程的基本操作

程序中经常会把需要同时执行的或耗时的任务放到单独的线程中执行。通常将执行的任务封装为一个 Runnable 的实现类，而通过 Thread 类开启一个线程，基本的程序结构如下：

{% highlight java linenos %}
// 封装任务的 Runnable 类
class MyRunnable implements Runnable {
    public void run() {
        /* task code */
    }
}

// 创建一个类对象
Runnable r = new MyRunnable();

// 有 Runnable 创建一个 Thread 对象
Thread t = new Thread(r);

// 启动线程
t.start();
{% endhighlight %}

### 中断线程

线程启动后有三种终止方式：1）执行完所有的代码后正常结束 2）发生未捕获的异常 3）收到中断信号后终止执行。

interrupt 方法可以用来请求终止线程。当对一个线程调用 interrupt 方法时，线程的中断状态将被置位。线程可以通过检查这个标志，以判断线程是否被中断。当对一个阻塞线程调用 interrupt 方法时，阻塞线程会被 InterruptedException 异常中断。中断仅仅是一个请求，被中断的线程可以决定如何响应。响应中断的线程具有如下形式：

{% highlight java linenos %}
public void run() {
    try {
        while (!Thread.currentThread().isInterrupted() && more work to do) {
            do more work
        }
    } catch (InterruptedException e) {
        // thread was interrupted during sleep or wait
    } finally {
        // do some cleanup
    }
}
{% endhighlight %}

当线程每次工作迭代后都调用可中断的阻塞方法时（如 sleep），便没有必要检测中断状态，捕获 InterruptedException 即可。

### 线程状态

每个线程都有六种状态，可以通过 getState 方法得到线程的状态。这六种状态分别是 NEW, RUNNABLE, BLOCKED, WAITING, TIMED WAITING, TERMINATED。它们之间的关系可以表示为下图：

![thread_state.jpg](http://user-image.logdown.io/user/3258/blog/3302/post/192621/IaD4R85Tsq0wQIkMSHc4_thread_state.jpg)

### 线程属性

线程包括各种属性：线程优先级、守护线程、线程组以及处理未捕获异常的处理器。

#### 线程优先级

每个线程有一个优先级。默认情况下，线程继承它的父线程的优先级。可以用 setPriority 方法设置一个线程的优先级。因为优先级的实现依赖于宿主系统，所以不要构建依赖于优先级的程序。

#### 守护线程

可以调用 t.setDaemon(ture) 将线程设置为守护线程，这一方法必须在线程启动之前调用。守护线程的唯一用途就是为其它线程提供服务。当只剩下守护线程时，虚拟机就会退出。因为守护线程可能在任何时候终止，所以不应该访问任何固有资源，如文件、数据库等。