# zookeeper学习笔记

4/9/2018 12:48:21 AM 


----------------------

从apache官方网站将最新版本的zookeeper下载下来后，将其上传至虚拟机，并解压，然后配置环境变量（/etc/environment）。

	# 编辑/etc/environment文件，添加zookeeper安装路径

	JAVA_HOME=/usr/soft/jdk1.8.0_161
	ZOOKEEPER_HOME=/usr/soft/zookeeper-3.4.11

	PATH=/usr/local/bin:/usr/local/sbin:/usr/bin:/usr/sbin:/bin:/sbin:/usr/soft/jdk1.8.0_161/bin:/usr/soft/hadoop-2.7.5/bin:/usr/soft/hadoop-2.7.5/sbin:/usr/soft/zookeeper-3.4.11/bin

## 完全分布式模式
> 主机分配：在hqq2、hqq3、hqq4主机上做集群

### 1、配置zoo.cfg文件
> 该文件默认不存在 ，可能通过复制官方提供的zoo_sample.cfg文件并命名为zoo.cfg，该文件需要配置以下几项内容：  
> 1、tickTime=2000              #心跳周期，默认2000ms  
> 2、dataDir=/tmp/zookeeper     #数据目录  
> 3、clientPort=2181            #server提供客户端连接的端口号
> 4、集群节点地址及端口号(3个节点)：  
>   # 第一个端口为leader的端口号，供其他follower节点连接，第二个端口当leader挂掉时，其他follower节点通过该端口相互访问协商选举新的leader:   
  server.2=hqq2:2888:3888      
  server.3=hqq3:2888:3888  
  server.4=hqq4:2888:3888  

进入${zookeeper_home}/conf/目录，复制zoo_sample.cfg文件，并命名为zoo.cfg:

	[hqq@hqq1 conf]$ cp zoo_sample.cfg zoo.cfg

以下是完整的zoo.cfg配置文件内容：

	# The number of milliseconds of each tick
	tickTime=2000
	
	# The number of ticks that the initial 
	# synchronization phase can take
	initLimit=10
	
	# The number of ticks that can pass between 
	# sending a request and getting an acknowledgement
	syncLimit=5
	
	# the directory where the snapshot is stored.
	# do not use /tmp for storage, /tmp here is just 
	# example sakes.
	dataDir=/tmp/zookeeper
	
	# the port at which the clients will connect
	clientPort=2181
	
	# the maximum number of client connections.
	# increase this if you need to handle more clients
	#maxClientCnxns=60
	
	#
	# Be sure to read the maintenance section of the 
	# administrator guide before turning on autopurge.
	#
	# http://zookeeper.apache.org/doc/current/zookeeperAdmin.html#sc_maintenance
	#
	# The number of snapshots to retain in dataDir
	#autopurge.snapRetainCount=3
	
	# Purge task interval in hours
	# Set to "0" to disable auto purge feature
	#autopurge.purgeInterval=1
	
	#第一个端口为leader的端口号，供其他follower节点连接
	server.2=hqq2:2888:3888     
	server.3=hqq3:2888:3888
	server.4=hqq4:2888:3888

最后将该配置文件通过远程复制方式，拷贝到其他节点主机上。

### 2、配置myid文件
> 该文件默认不存在，需要自己手动在各个节点上创建，该文件存放的路径在zoo.cfg文件中配置dataDir指定的目录里，所以根据配置情况，到指定的目录中手动创建该文件（如/tmp/zookeeper/目录）

	# 在/tmp/目录下创建zookeeper目录
	[hqq@hqq1 tmp]$ mkdir zoopeeker

	# 在/tmp/zookeeper/目录中创建myid文件并在文件中写入当前节点的id，该id即为前面server.n中的n。
	[hqq@hqq1 zoopeeker]$ echo 1 > myid


### 3、启动zookeeper
> 启动之前确认各个节点主机上的zookeeper以及配置文件（除了myid文件）都正确配置并保持一致，包括配置环境变量。

	# 启动zookeeper,确保已配置ZOOKEEPER_HOME环境变量，否则需要进入到zookeeper安装目录bin目录中启动
	[hqq@hqq1 ~]$ zkServer.sh start

	# 查看当前节点zookeeper状态
	[hqq@hqq1 ~]$ zkServer.sh status

### 4、验证
> 模拟将leader节点挂掉，查看其他两个follower节点会不会重新选举出一个leader节点出来。  
> 通过zkServer.sh status查看状态中可知道哪个节点是leader哪些节点是follower，在leader节点中，通过jps查看进程(QuorumPeerMain)，并通过kill指令将zookeeper的进程QuorumPeerMain杀死掉。  
> 然后再从其他节点中查看状态，验证是否会重新选出一个新的leader节点出来。

### 停止zookeeper

	[hqq@hqq1 ~]$ zkServer.sh stop