# hadoop学习笔记

3/3/2018 9:37:25 PM 

--------------------------------

## 使用链接符号方式实现配置分离

由于hadoop在启动时，如果不使用--config参数指定启动目录的话，则默认从/usr/soft/hadoop-2.7.5/etc/hadoop/目录中加载。因为目前在/usr/soft/hadoop-2.7.5/etc/目录下，我们复制了一份hadoop目录并重命名为hadoop_pseudo，并修改了配置文件core-site.xml等四个文件成为伪分布模式（保留了原本的独立模式）。  
为了依然使用hadoop本身默认的启动方式，我们需要将hadoop目录（独立模式）重命名成hadoop_alone，然后在/usr/soft/hadoop-2.7.5/etc/目录下创建一符号连接文件hadoop，并指向到具体需要启动的模式目录（类似windows的快捷方式）。比如hadoop以伪分布模式启动，则创建hadoop连接符号文件并指向到hadoop_pseudo目录即可，启动时就不需要使用--config来指定目录了。

	# 将独立模式的hadoop目录重命名成hadoop_alone
	[hqq@study etc]$ mv hadoop hadoop_alone

	#创建连接符号文件hadoop并指向到hadoop_pseudo目录
	[hqq@study etc]$ ln -s hadoop_pseudo hadoop

	#查看是否创建成功
	[hqq@study etc]$ ll
	总用量 8 
	lrwxrwxrwx. 1 hqq hqq   13 3月   3 21:52 hadoop -> hadoop_pseudo  <== 连接符号文件
	drwxr-xr-x. 2 hqq hqq 4096 12月 16 09:12 hadoop_alone
	drwxr-xr-x. 2 hqq hqq 4096 3月   3 00:49 hadoop_pseudo



