---
layout: post
title: '[黑马程序员]流与文件'
date: 2014-04-07 11:50
comments: true
categories: 
---
流是对读取字节序列的来源或输出字节序列的目的地的一个抽象，Java 标准库提供了常用的类用于输入输出和文件系统的操作。

### 字节流

计算机都是以字节的形式存储数据的，字节流就是用于读取原生字节的，因此所有的 IO 操作都可以通过字节流完成。InputStream 和 OutputStream 构成了字节流类的基础，定义了基本的用于读入或写出字节序列的方法。但基本的字节流工作在非常低的层次上，为了提高输入输出效率以及更方便的使用，Java 标准库提供了过滤器流，通过组合过滤器流，可以得到更高效的输入输出流和更方便的方法。字节流的基本层次结构可以概括为下图：

![InputStream.jpg](http://user-image.logdown.io/user/3258/blog/3302/post/192722/9mfogodHQFWO4NrKfRuU_InputStream.jpg)

![OutputStream.jpg](http://user-image.logdown.io/user/3258/blog/3302/post/192722/zMHRSRUSv6skDEYcZm35_OutputStream.jpg)

流过滤器类通过封装基本的字节流提供了更强大的功能，例如下面的代码可以直接读取数值类型以及通过提供输入缓冲来提高读取效率：

{% highlight java linenos %}
FileInputStream fin = new FileInputStream("data_file.txt");
BufferedInputStream bin = new BufferedInputStream(fin);
DataInputStream din = new DataInputStream(bin);
double s = din.readDouble();
{% endhighlight %}

对象流通过对象序列化机制允许我们使用 readObject 或 writeObject 直接读取对象或将对象写入流中。

### 字符流

字符串是程序中最常用的数据类型之一，为了方便地通过输入输出流操作字符数据，Java 类库提供了字符流。其中最基本的两个类是 Reader 和 Writer。字符流有着同字节流类似的过滤器组合机制。此外，InputStreamReader 和 OutputStreamWriter 允许我们通过字节流得到字符流。下图总结了字节流的层次结构：

![reader.jpg](http://user-image.logdown.io/user/3258/blog/3302/post/192722/RlPMyhQVGPCqcEBGo2wy_reader.jpg)

![writer.jpg](http://user-image.logdown.io/user/3258/blog/3302/post/192722/6pANHNffSomDDoLTP46L_writer.jpg)

### 文件系统

字节流与字符流都是用于操作文件内容的，Java 类库还提供了管理文件系统的类和方法。File 类是对文件系统路径的一个抽象，它既可以表示目录页可以表示文件，我们可以使用 File 类对象创建、删除、重命名、移动一个文件，也可以查看或修改文件属性，下面这段代码展示了如何使用 File 类来遍历一个目录下的文件并打印出目录树结构：

{% highlight java linenos %}
public class FileTest {
	public static void main(String[] args) throws IOException {
  	String pathname = "path_name";
		File dir = new File(pathname);
		getDirStruct(dir);
	}
	
	public static void getDirStruct(File dir) throws IOException {
		if (!dir.isDirectory()) {
			return;
		}
		
		dir = dir.getCanonicalFile();

		System.out.println(dir.getPath() + File.separator);
		getDirStruct(dir, 1);
	}
	
	private static void getDirStruct(File dir, int level) {
		for (File file : dir.listFiles()) {
			if (file.isDirectory()) {
				System.out.format("%" + level * 2 + "s%s%s%n", "", file.getName(), File.separator);
				getDirStruct(file, level + 1);
			} else {
				System.out.format("%" + level * 2 + "s%s%n", "", file.getName());
			}
		}
	}
}
{% endhighlight %}