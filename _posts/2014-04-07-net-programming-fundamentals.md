---
layout: post
title: '[黑马程序员]网络编程基础'
date: 2014-04-07 14:13
comments: true
categories: 
---
Java 提供了基本的网络编程接口，封装了网络的底层细节，使我们能够方便地写出网络应用程序。

### IP 地址

Java 中使用 InetAddress 封装了网络地址，可以使用它的静态方法 InetAddress.getByName(String host) 获得一个实例对象。

### Socket API

操作系统向用户提供了网络编程接口 Socket，Java 类库在其基础上进行了进一步封装，提供个功能强大的类和方法。Socket API 基于两种不同的底层协议：TCP, UDP。Java 类库也针对这两种不同的底层协议提供了相关的类，比如针对 UDP 服务的 DatagramSocket, DatagramPacket 以及针对 TCP 服务的 Socket, ServerSocket。下面就详细地介绍如何使用这些类进行网络编程。

### TCP 编程

TCP 指安全的、面向连接的网络服务，一端为服务器，一端为客户端。在客户端可以使用 Socket 获得编程接口并连接到指定的服务器，连接成功后，通过 getInputStream 和 getOutputStream 获得网络的输入输出流进行数据传输。在服务器端使用 ServerSocket 绑定 Socket 接口并调用 accept 方法监听连接请求，一旦成功建立了连接，便可以通过获得的 Socket 对象进行数据传输。下面是一个简单的服务器端程序：

{% highlight java linenos %}
public class EchoServer {
	public static void main(String[] args) throws IOException {
		try (ServerSocket ss = new ServerSocket(8189)) {
			try (Socket incoming = ss.accept()) {
				Scanner in = new Scanner(incoming.getInputStream());
				PrintWriter out = new PrintWriter(incoming.getOutputStream(), true);
				
				out.println("hello world");
				
				boolean done = false;
				while (!done && in.hasNextLine()) {
					String line = in.nextLine();
					out.println("Echo: " + line);
					if (line.trim().equals("end")) {
						done = true;
					}
				}
			}
		}
	}
}
{% endhighlight %}

### UDP 编程

UDP 是无连接的数据包服务，它以数据包的形式发送数据。可以使用 DatagramPacket 封装数据包，用 DatagramSocket 的 receive 和 send 方法接收或发送数据包。下面是一个简单的发送接收数据包的程序：

{% highlight java linenos %}
// 接收端
public class Receiver {
    public static void main(String[] args) throws IOException {
        DatagramSocket s = new DatagramSocket(6789);
        
        byte[] buf = new byte[1000];
        DatagramPacket packet = new DatagramPacket(buf, buf.length);
        s.receive(packet);
        System.out.println(new String(buf).trim());
    }
}
{% endhighlight %}
{% highlight java linenos %}
// 发送端
public class Sender {
    public static void main(String[] args) throws IOException {
        InetAddress dest = InetAddress.getByName("localhost");
        DatagramSocket s = new DatagramSocket(2345);
        
        Scanner in = new Scanner(System.in);
        String msg = in.nextLine();
        in.close();
        
        DatagramPacket packet = new DatagramPacket(msg.getBytes(),
            msg.getBytes().length, dest, 6789);
        s.send(packet);
    }
}
{% endhighlight %}