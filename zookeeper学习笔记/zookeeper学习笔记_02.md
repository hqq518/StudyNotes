# zookeeper学习笔记

4/12/2018 11:39:24 PM 



--------------------------

## 四字符客户端指令
> 在hqq1上（非zookeeper集群节点），通过zookeeper提供的四字指令并配合Linux自带的nc指令，实现在客户端（非集群主机）上查看集群状态等信息。

### 查看hqq2节点zk的配置信息：conf

	[hqq@hqq1 ~]$ echo conf | nc hqq2 2181
	clientPort=2181
	dataDir=/tmp/zookeeper/version-2
	dataLogDir=/tmp/zookeeper/version-2
	tickTime=2000
	maxClientCnxns=60
	minSessionTimeout=4000
	maxSessionTimeout=40000
	serverId=2
	initLimit=10
	syncLimit=5
	electionAlg=3
	electionPort=3888
	quorumPort=2888
	peerType=0

### 询问hqq2节点zk状态是否正常：ruok  (are you ok)

	[hqq@hqq1 ~]$ echo ruok | nc hqq2 2181
	imok

### 查看hqq2节点环境信息：envi

	[hqq@hqq1 ~]$ echo envi | nc hqq2 2181
	Environment:
	zookeeper.version=3.4.11-37e277162d567b55a07d1755f0b31c32e93c01a0, built on 11/01/2017 18:06 GMT
	host.name=hqq2
	java.version=1.8.0_161
	java.vendor=Oracle Corporation
	java.home=/usr/soft/jdk1.8.0_161/jre
	java.class.path=/usr/soft/zookeeper-3.4.11/bin/../build/classes:/usr/soft/zookeeper-3.4.11/bin/../build/lib/*.jar:/usr/soft/zookeeper-3.4.11/bin/../lib/slf4j-log4j12-1.6.1.jar:/usr/soft/zookeeper-3.4.11/bin/../lib/slf4j-api-1.6.1.jar:/usr/soft/zookeeper-3.4.11/bin/../lib/netty-3.10.5.Final.jar:/usr/soft/zookeeper-3.4.11/bin/../lib/log4j-1.2.16.jar:/usr/soft/zookeeper-3.4.11/bin/../lib/jline-0.9.94.jar:/usr/soft/zookeeper-3.4.11/bin/../lib/audience-annotations-0.5.0.jar:/usr/soft/zookeeper-3.4.11/bin/../zookeeper-3.4.11.jar:/usr/soft/zookeeper-3.4.11/bin/../src/java/lib/*.jar:/usr/soft/zookeeper-3.4.11/bin/../conf:
	java.library.path=/usr/java/packages/lib/amd64:/usr/lib64:/lib64:/lib:/usr/lib
	java.io.tmpdir=/tmp
	java.compiler=<NA>
	os.name=Linux
	os.arch=amd64
	os.version=3.10.0-693.17.1.el7.x86_64
	user.name=hqq
	user.home=/home/hqq
	user.dir=/home/hqq

### 其他指令：
	
	cons	//连接信息
	dump	//未处理会话节点
	reqs	//未处理请求
	stat	//统计信息
	wchs	//服务器watch的详细信息
	wchp	//列出指定路径下的服务器信息


## zkCli.sh客户端程序
> zookeeper除了提供有四字符指令外，还提供了一个shell程序zkCli.sh给客户端连接集群某一节点查看集群信息。该程序需要在客户端也要安装有zookeeper（仅仅是安装，不需要启动，因为zkClik.sh是zk里的一个程序）。  
> 指标格式： zkCli.sh -server ip:2181

### 在hqq1上通过zkCli.sh连接到hqq2节点：

	[hqq@hqq1 ~]$ zkCli.sh -server hqq2:2181
	...  (最后按一下回车)

	# 查看帮助信息: h
	[zk: hqq2:2181(CONNECTED) 0] h
	ZooKeeper -server host:port cmd args
		stat path [watch]			# 查看目录状态信息
		set path data [version]		# 设置或修改目录数据
		ls path [watch]				# 列出指定路径目录及子目录
		delquota [-n|-b] path
		ls2 path [watch]
		setAcl path acl
		setquota -n|-b val path
		history 
		redo cmdno
		printwatches on|off
		delete path [version]		# 删除指定路径的目录
		sync path
		listquota path
		rmr path
		get path [watch]			# 获取指定路径目录数据
		create [-s] [-e] path data acl	# 创建目录
		addauth scheme auth
		quit 
		getAcl path
		close 						# 关闭连接
		connect host:port			# 连接到指定主机和端口

### 列出zk节点上的根目录下的子目录：

	[zk: hqq2:2181(CONNECTED) 1] ls /
	[zookeeper]

### 创建目录：

	# 在根目录下创建root目录并绑定“root_data”字符串数据到该目录上
	[zk: hqq2:2181(CONNECTED) 2] create /root root_data 
	Created /root

	# 列出根目录下的所有子目录（zookeeper是默认存在的）
	[zk: hqq2:2181(CONNECTED) 3] ls /
	[zookeeper, root]

### 获取指定目录绑定的数据：

	# 获取绑定在/root目录上的数据
	[zk: hqq2:2181(CONNECTED) 4] get /root
	root_data				<=== 该数据是在创建root目录时绑定上去的
	cZxid = 0x300000002
	ctime = Fri Apr 13 00:30:01 CST 2018
	mZxid = 0x300000002
	mtime = Fri Apr 13 00:30:01 CST 2018
	pZxid = 0x300000002
	cversion = 0
	dataVersion = 0
	aclVersion = 0
	ephemeralOwner = 0x0
	dataLength = 9
	numChildren = 0

### 在/root目录下创建一个子目录并绑定数据：

	# 在/root目录下创建s1子目录并绑定数据（不能同时创建多级目录，只能一层一层目录创建）
	[zk: hqq2:2181(CONNECTED) 7] create /root/s1 s1_data
	Created /root/s1

	# 查看/root目录下的子目录
	[zk: hqq2:2181(CONNECTED) 8] ls /root
	[s1]


> zookeeper集群中各节点的目录及数据是一致的，也就是说当你在某个节点上创建目录并绑定数据到目录时，zk会将你创建的目录和数据信息同步到集群中其他节点上，以保证集群中所有节点数据相一致。







