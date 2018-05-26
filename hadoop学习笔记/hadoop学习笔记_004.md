# hadoop学习笔记

3/13/2018 11:17:24 PM 

--------------------

## 在集群环境中运行统计天气数据程序

### 在hadoop文件系统中创建目录：

	[hqq@hqq1 ~]$ hadoop fs -mkdir -p /user/hqq/ncdc_data

### 上传天气数据文件到hadoop文件系统：

> 首先确保在集群环境下所有主机已关闭防火墙，若不关闭防火墙可能导致上传文件到hadoop的分布式文件系统失败。

	#关闭防火墙（在CentOS7下）
	[hqq@hqq1 ~]$ systemctl stop firewalld.service

	# 上传以.gz结尾的文件到hadoop文件系统指定目录中（请确保hadoop已正确启动）
	[hqq@hqq1 data]$ hadoop fs -put *.gz /user/hqq/ncdc_data/


### 上传程序jar到CentOS7中并启动程序：

在本地使用eclipse开发完程度后，通过导出jar文件（只导出原程序，lib目录的jar包不需要导出）。在导出jar文件过程中需要指定main方法所在的java文件。

	# 在当前用户的主目录下创建一个download目录
	[hqq@hqq1 ~]$ mkdir download

	# 将jar文件通过共享目录上传到download目录中
	[hqq@hqq1 ~]$ cp /mnt/hgfs/share/hadoop_demo2.jar ~/downlad

	# 在download目录下通过hadoop启动jar文件程序（请确保hadoop已正确启动）
	# 其中/user/hqq/ncdc_data是天气数据文件所在目录，/user/hqq/out是hadoop输出目录（该目录不能手动创建）
	[hqq@hqq1 downlad]$ hadoop jar hadoop_demo2.jar /user/hqq/ncdc_data /user/hqq/out

	# 当程序运行完成后，通过hadoop查看目录，可发现多一个out目录
	[hqq@hqq1 ~]$ hadoop fs -ls -R /
	drwxr-xr-x   - hqq supergroup          0 2018-03-13 23:14 /user
	drwxr-xr-x   - hqq supergroup          0 2018-03-14 00:29 /user/hqq
	drwxr-xr-x   - hqq supergroup          0 2018-03-14 00:12 /user/hqq/ncdc_data
	...
	drwxr-xr-x   - hqq supergroup          0 2018-03-14 00:37 /user/hqq/out
	-rw-r--r--   2 hqq supergroup          0 2018-03-14 00:37 /user/hqq/out/_SUCCESS
	-rw-r--r--   2 hqq supergroup         36 2018-03-14 00:37 /user/hqq/out/part-r-00000

	# 其中/user/hqq/out/part-r-00000文件即是hadoop输出统计结果
	[hqq@hqq1 ~]$ hadoop fs -cat /user/hqq/out/part-r-*
	1950	302
	2015	254
	2016	285
	2017	220


## CentOS7设置以文本模式启动
	
	# 1、查看当前CentOS7支持的启动模式：
	[root@hqq1 ~]# cat /etc/inittab
	# inittab is no longer used when using systemd.
	#
	# ADDING CONFIGURATION HERE WILL HAVE NO EFFECT ON YOUR SYSTEM.
	#
	# Ctrl-Alt-Delete is handled by /usr/lib/systemd/system/ctrl-alt-del.target
	#
	# systemd uses 'targets' instead of runlevels. By default, there are two main targets:
	#
	# multi-user.target: analogous to runlevel 3 --->文本模式
	# graphical.target: analogous to runlevel 5 --->图形模式
	#
	# To view current default target, run:
	# systemctl get-default
	#
	# To set a default target, run:
	# systemctl set-default TARGET.target
	#

	# 2、查看当前默认的启动模式：
	[root@hqq1 ~]# systemctl get-default
	graphical.target		---> 当前默认以图形界面模式启动

	# 3、设置默认启动模式为文本模式：
	[root@hqq1 ~]# systemctl set-default multi-user.target
	Removed symlink /etc/systemd/system/default.target.
	Created symlink from /etc/systemd/system/default.target to /usr/lib/systemd/system/multi-user.target.

	# 4、重启系统
	[root@hqq1 ~]# reboot