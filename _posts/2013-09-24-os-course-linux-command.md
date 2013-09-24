---
layout: post
title: 操作系统实验——Linux常用命令
---

1.  查看当前登录在系统中的用户列表, 系统中的用户总数和系统启动时间

	    # show who is logged on and what they are doing
	    $ w

	    # show who is logged on
	    $ who

	    # get number of users in a system
	    $ wc -l /etc/passwd

	    # tell how long the system has been running
	    $ uptime

2.  将系统文件 /etc/profile 复制到主用户目录, 并改名为 profile.txt, 查看此文件的内容, 并对非空行进行编号; 重新打开此文件, 从 profile 的第5行开始显示, 每屏幕仅显示5行

	    $ cp /etc/profile ~/profile.txt
	    $ cat -b profile.txt
	    $ tail -n +5 profile.txt | more -5

3.  在主用户目录创建临时目录 tmp, 在此目录下, 将 /etc 目录压缩成 etc.tar.bz2 文件, 然后解压缩

	    $ cd ~
	    $ mkdir tmp
	    $ cd tmp
	    $ tar -jcvf etc.tar.bz2 /etc
	    $ tar -jxvf etc.tar.bz2

4.  查找 /etc 目录下包含字符串 "ss" 的文件; 复制 /etc/passwd 文件到用户的主目录下, 搜索这个文件中包含字符串 "root" 的行, 并显示行号

	    $ ls -ld /etc/*ss*
	
	    $ cp /etc/passwd ~
	    $ grep -n "root" passwd

5.  创建一个新用户 user1, 给该用户设置密码为 LoveLinux, 将用户名更改为 user2; 创建 user3, 将 user3 的有效组切换为 admin; 切换到 user3, 在 /home 目录下创建 dir/ 目录; 切换到 user2, 查看 user2 是否可以在 dir 目录下创建、删除文件, 如果不可以, 修改这个目录的权限, 或者修改这个目录的所有者、所属组, 使得用户 user2 可以在这个目录下创建、删除文件

	    $ useradd user1
	    $ passwd user1	# set initial password according to prompt
	    $ usermod -l user2 user1

	    $ useradd -g admin user3
	    $ passwd user3
	    $ su user3
	    $ sudo mkdir /home/dir
	    $ su user2
	    $ sudo chown user2 /home/dir
	    $ touch /home/dir/demo
	    $ rm /home/dir/demo
