---
layout: post
title: '交通灯信号管理系统'
date: 2014-04-07 11:02
comments: true
categories: 
---
模拟实现十字路口的交通灯管理系统逻辑，具体需求如下：

    - 异步随机生成按照各个路线行驶的车辆。
    - 信号灯忽略黄灯，只考虑红灯和绿灯。
    - 应考虑左转车辆控制信号灯，右转车辆不受信号灯控制。
    - 每辆车通过路口时间为1秒。
    - 随机生成车辆时间间隔以及红绿灯交换时间间隔自定，可以设置。
    - 只考虑系统逻辑实现。

面向对象的设计分析及程序框架：

{% highlight java linenos %}
/**
 * 每个Road对象代表一条路线，总共有12条路线，即系统中总共要产生12个Road实例对象。
 * 每条路线上随机增加新的车辆，增加到一个集合中保存。
 * 每条路线每隔一秒都会检查控制本路线的灯是否为绿，是则将本路线保存车的集合中的第一辆车移除，即表示车穿过路口。
 */
public class Road {
    private List<String> vehicles;
    
    public Road() {
        //模拟车辆不断随机上路的过程
        ...
        
        //每隔一秒检查对应的灯是否为绿，是则放行一辆车	
        ...
    }
}
{% endhighlight %}
{% highlight java linenos %}
/**
 * 每个Lamp元素代表一个方向上的灯，总共有12个方向，所有总共有12个Lamp元素。
 * 每两个形成一组，一组灯同时变绿或变红。
 * 由于从南向东和从西向北、以及它们的对应方向不受红绿灯的控制，所以，可以假想它们总是绿灯。
 */
public enum Lamp {
    /*每个枚举元素各表示一个方向的控制灯*/
    ...
    
    /* 某个灯变绿时，它对应方向的灯也要变绿 */
    public void light();
    
    /* 某个灯变红时，对应方向的灯也要变红，并且下一个方向的灯要变绿 */
    public Lamp blackOut();
}
{% endhighlight %}
{% highlight java linenos %}
// 信号灯控制器
public class LampController {
    public LampController() {
        /*每隔10秒将当前绿灯变为红灯，并让下一个方向的灯变绿*/
    }
}
{% endhighlight %}