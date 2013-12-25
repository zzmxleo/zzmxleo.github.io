---
layout: post
title: Markdown 基本语法
---

块级元素
--------

### 段落和换行 ###

一行或多行连续的文字组成一个段落，段落之间通过一行或多行的空白行分隔（只含有空格和制表符的行也被认为是空白行）。普通的段落不应被缩进。当在段落中需要换行时（插入 `<br />` 标签），可以在行末输入两个或多个空格作为结束。


### 标题 ###

Markdown 中有两种插入标题的方式，第一种是在标题下使用 `=` 和 `-`。例如：

{% highlight text %}
这是一级标题
============

这是二级标题
------------
{% endhighlight %}

另一种方式是在行首输入1-6个 `#`，例如：

{% highlight text %}
# 这是一级标题
    
## 这是二级标题

### 这是三级标题
{% endhighlight %}

### 块级引用 ###

Markdown 使用 `>` 表示块级引用，块级引用中段落和换行规则依然适用。例如：

{% highlight text %}
> This is a blockquote with two paragraphs. Lorem ipsum dolor sit amet,
> consectetuer adipiscing elit. Aliquam hendrerit mi posuere lectus.
> Vestibulum enim wisi, viverra nec, fringilla in, laoreet vitae, risus.
> 
> Donec sit amet nisl. Aliquam semper ipsum sit amet velit. Suspendisse
> id sem consectetuer libero luctus adipiscing.
{% endhighlight %}

### 列表 ###

Markdown 支持有序列表和无序列表。无序列表使用 `*`，`+` 或 `-` 作为标识，例如：

{% highlight text %}
* Red
* Green
* Blue

+ Red
+ Green
+ Blue

- Red
- Green
- Blue
{% endhighlight %}

有序列表使用数字和 `.` 作为标识：

{% highlight text %}
1. Red
2. Green
3. Blue
{% endhighlight %}
