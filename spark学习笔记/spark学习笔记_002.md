# spark学习笔记

5/13/2018 8:43:39 PM 

## 独立集群模式（standalone cluster mode）
> 集群模式中需要启动一个master节点，以及其他的worker节点。其中，在哪个节点上启动spark，则默认以哪个节点为master节点，同时还需要配置指定哪些节点为worker节点。  
> 现在部署拓扑结构：hqq1作为master节点，hqq2、hqq3、hqq4分别作为worker节点。

### 在hqq1主机上配置worker节点列表： conf/slaves
> 官方并没有直接提供有slaves文件，而是提供了slaves.template文件，我们需要将该文件复制并重命名为slaves文件。然后打开该文件，输入worker节点列表：

	# 将slaves.template文件复制并重命名为slaves文件
	[hqq@hqq1 conf]$ cp slaves.template slaves

	# 编辑slaves文件并输入worker节点列表
	[hqq@hqq1 conf]$ vim slaves

	# A Spark Worker will be started on each of the machines listed below.
	hqq2
	hqq3
	hqq4
		

### 将conf/slaves文件远程复制到其他节点上

	[hqq@hqq1 conf]$ scp slaves hqq@hqq2:/usr/soft/spark-2.3.0-bin-hadoop2.7/conf/
	[hqq@hqq1 conf]$ scp slaves hqq@hqq3:/usr/soft/spark-2.3.0-bin-hadoop2.7/conf/
	[hqq@hqq1 conf]$ scp slaves hqq@hqq4:/usr/soft/spark-2.3.0-bin-hadoop2.7/conf/

### 在hqq1主机上启动master进程
> 进入${spark_home}/sbin目录，该目录中存放启动或停止spark的命令。

	# 启动master节点
	[hqq@hqq1 sbin]$ ./start-master.sh 
	starting org.apache.spark.deploy.master.Master, logging to /usr/soft/spark-2.3.0-bin-hadoop2.7/logs/spark-hqq-org.apache.spark.deploy.master.Master-1-hqq1.out

	# 查看java进程，发现多出Master
	[hqq@hqq1 sbin]$ jps
	20993 Master
	21049 Jps

### 启动worker进程
> 启动worker进程，可以单独在每个worker节点主机上启动start-slave.sh，同时指定master节点的ip及端口（默认7077）

	# 在hqq2节点主机上，进入${spark_home}/sbin/目录后，执行以下命名
	[hqq@hqq2 sbin]$ ./start-slave.sh spark://hqq1:7077

	# worker进程启动后，在master节点（hqq1）上查看端口，可以看到有worker节点连接上来
	[hqq@hqq1 sbin]$ netstat -ano | grep 7077
	tcp6       0      0 192.168.0.201:7077      :::*                    LISTEN      off (0.00/0/0)
	tcp6       0      0 192.168.0.201:7077      192.168.0.202:44838     ESTABLISHED off (0.00/0/0)

### 停止worker进程

	[hqq@hqq2 sbin]$ ./stop-slave.sh

### 通过在master节点上启动所有worker节点进程
> 由于worker节点比较多，一个个单独到相应的节点主机上启动worker进程比较麻烦，可以通过在marster主机上，同步将所有的worker节点全部启动起来（spark从master主机通过无密登录到其他worker主机上后再启动worker进程）。

	# 在hqq1主机（master）上启动所有的worker节点的进程
	[hqq@hqq1 sbin]$ ./start-slaves.sh 
	
	
	# 所有worker节点启动完成后，可在各个节点上查看java进程发现有Worker进程在运行，表示启动成功
	[hqq@hqq2 ~]$ jps
	3124 Jps
	3079 Worker

### 一次启动master和所有的worker节点
> 除了上面介绍的启动master和worker节点的方式以外，spark还提供有一个命令可以一次性将master和worker节点启动进来（在master节点上执行以下命令）：

	# 启动所有的spark节点（master和workers）
	[hqq@hqq1 sbin]$ ./start-all.sh

	# 停止所有的spark节点（master和workers）
	[hqq@hqq1 sbin]$ ./stop-all.sh


### 通过web查看spark信息
> master节点默认的web端口号是8080，如http://hqq1:8080   
> master节点默认的程序连接端口号是7077,如spark://hqq1:7077
> worker节点默认的web端口号是8081，如http://hqq2:8081  

-------------------------
### 在spak集群环境上运行spark-shell.sh程序
> 在前面介绍过如何在spark本地模式下运行spark-shell.sh程序，现在我们已经搭建好了一个spakr集群环境，所以可以在这个集群环境上运行spark-shell.sh程序。  
> 在任意一个节点上都可以运行spark-shell.sh程序，但需要指定master节点的地址及端口(默认7077)。  
> 启动spark-shell.sh程序后，在以master的web页面上查看到Running Applications中多出一个正在运行的应用，也就是这个spark-shell。

	[hqq@hqq1 sbin]$ spark-shell --master spark://hqq1:7077

	# 另外还可以先设置MASTER变量之后，可以直接使用spark-shell命令而不需要增加 --master参数
	[hqq@hqq1 sbin]$ export MASTER=spark://hqq1:7077
	# 直接启动spark-shell
	[hqq@hqq1 sbin]$ spark-shell

	

	

