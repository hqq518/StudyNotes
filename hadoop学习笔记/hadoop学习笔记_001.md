# hadoop学习笔记

3/1/2018 12:23:14 AM 

---------------------------

## 安装JDK

1、从[oracle官网](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)下载Linux 64位版本的jdk（jdk-8u161-linux-x64.tar.gz），并上传到CentOS的挂载目录/mnt/hgfs/中，然后将jdk安装文件复制到/usr/soft/目录中（soft目录需要自己创建）并解压。

2、配置环境变量，打开/etc/environment文件，在该文件中添加JAVA_HOME变量：

	JAVA_HOME=/usr/soft/jdk1.8.0_161
	PATH=/usr/local/bin:/usr/local/sbin:/usr/bin:/usr/sbin:/bin:/sbin:/home/hqq/.local/bin:/home/hqq/bin:/usr/soft/jdk1.8.0_161/bin

3、执行source命令使/etc/environment文件立即生效：
	
	[root@study ~]$ source /etc/environment

	#测试jdk是否安装成功
	[root@study ~]$ java -version
	openjdk version "1.8.0_161"
	OpenJDK Runtime Environment (build 1.8.0_161-b14)
	OpenJDK 64-Bit Server VM (build 25.161-b14, mixed mode)



## 安装hadoop

1、从[apache官网](http://hadoop.apache.org/releases.html)下载最新版本hadoop二进制安装文件[hadoop-3.0.0.tar.gz](http://www.apache.org/dyn/closer.cgi/hadoop/common/hadoop-3.0.0/hadoop-3.0.0.tar.gz)，并上传到CentOS的挂载目录/mnt/hgfs/中，然后将该安装文件复制到/usr/soft/目录中并解压。

2、配置环境变量，打开/etc/environment文件，在该文件中添加HADOOP_INSTALL变量：

	JAVA_HOME=/usr/soft/jdk1.8.0_161
	HADOOP_INSTALL=/usr/soft/hadoop-3.0.0	#这里是自定义的变量
	PATH=/usr/local/bin:/usr/local/sbin:/usr/bin:/usr/sbin:/bin:/sbin:/home/hqq/.local/bin:/home/hqq/bin:/usr/soft/jdk1.8.0_161/bin:/usr/soft/hadoop-3.0.0/bin:/usr/soft/hadoop-3.0.0/sbin
	
3、执行source命令使/etc/environment文件立即生效：
	
	[root@study ~]$ source /etc/environment

	#测试hadoop是否安装成功
	[hqq@study .ssh]$ hadoop version
	Hadoop 3.0.0
	Source code repository https://git-wip-us.apache.org/repos/asf/hadoop.git -r c25427ceca461ee979d30edd7a4b0f50718e6533
	Compiled by andrew on 2017-12-08T19:16Z
	Compiled with protoc 2.5.0
	From source with checksum 397832cb5529187dc8cd74ad54ff22
	This command was run using /usr/soft/hadoop-3.0.0/share/hadoop/common/hadoop-common-3.0.0.jar

	
## hadoop伪分布模式

### 复制hadoop副本

进入/usr/soft/hadoop-3.0.0/etc目录，将该目录中的hadoop文件夹复制一份（为了不修改hadoop默认的配置，使独立模式与伪分布式模式共存）并命名为dadoop_pseudo：

	[hqq@study etc]$ cp -r hadoop hadoop_pseudo


### 配置*-site.xml文件

1、进入hadoop_pseudo文件夹中，打开core-site.xml、hdfs-site.xml、mapred-site.xml、yarn-site.xml四个文件（这四个文件都未配置有任何东西），可以使用图形界面的编辑器同时打开或使用vim命令也可以：

	[hqq@study hadoop_pseudo]$ gedit *-site.xml

2、在打开core-site.xml文件中添加如下配置项：

	<configuration>
		<property>
		    <name>fs.defaultFS</name>
		    <value>hdfs://localhost/</value>   
		</property>
	</configuration>

3、在打开的hdfs-site.xml文件中添加如下配置项：

	<configuration>
		<property>
		   <name>dfs.replication</name>  
		   <value>1</value>  <!--设置副本数1，不是默认是3份-->  
	   </property>
	</configuration>

4、在打开的mapred-site.xml文件中添加如下配置项：

	<configuration>
		<property>  
			<name>mapreduce.framework.name</name>  
			<value>yarn</value>  
		</property>  
	</configuration>

5、在打开的yarn-site.xml文件中添加如下配置项：

	<configuration>
		<property>  
			<name>yarn.resourcemanager.hostname </name>  
			<value>localhost</value>  
		</property>  
		<property>  
			<name>yarn.nodemanager.aux-services </name>  
			<value>mapreduce_shuffle</value>  
		</property>  
	</configuration>

===========================================

**特别说明一下：**    
以上几个xml文件的配置是按照大数据徐老师的视频里讲解的，视频中他的环境是Ubuntu而我虚拟机安装的环境是CentOS7。所以上面按照徐老师的解读配置后，我本地无法正常启动namenode服务，也就导致通过浏览器访问http://localhost:50070时无法访问，通过jps查看java进程时，也没有看到NameNode进程，后来参与这往篇文章[《Linux下部署Hadoop伪分布模式》](http://blog.csdn.net/a19881029/article/details/19084009/)后，按照以下方式配置就可以正常启动了：

	##### core-site.xml文件
	<configuration>  
		<property>  
			<name>fs.default.name</name>  
			<value>hdfs://localhost:9000</value>  
		</property>  
		<property>  
			<name>hadoop.tmp.dir</name>  
			<value>/home/hqq/hadooptmp</value>  
		</property>  
	</configuration>

	##### mapred-site.xml文件
	<configuration>
		<property>  
			<name>mapreduce.framework.name</name>  
			<value>yarn</value>  
		</property>  
		<property>  
			<name>mapred.job.tracker</name>  
			<value>localhost:9001</value>  
		</property>
	</configuration>

还是有一个问题就是当时从别的地方复制core-site.xml文件过来后，没有发现该文件的拥有者和用户组都是root，后来修改为hqq，用户组也修改为hqq了。不太清理之前按照徐老师的配置启动不正常是不是由该原因引起的。

=============================================

### 配置ssh

由于在伪分布或完全分布模式下，主机需要与其他节点（伪分布时其他节点也是自己）通信，故需要配置免密登录的ssh。  

**查看CentOs是否已安装ssh，若没安装则先安装ssh软件：**

	rpm -qa|grep -E "openssh"

若没有包含以下软件列表，则需要安装完整的ssh:

- openssh-ldap-6.6.1p1-35.el7_3.x86_64 
- openssh-clients-6.6.1p1-35.el7_3.x86_64 
- openssh-6.6.1p1-35.el7_3.x86_64 
- openssh-askpass-6.6.1p1-35.el7_3.x86_64 
- openssh-server-6.6.1p1-35.el7_3.x86_64 
- openssh-keycat-6.6.1p1-35.el7_3.x86_64 
- openssh-server-sysvinit-6.6.1p1-35.el7_3.x86_64

**安装缺失的软件：**

	sudo yum install openssh*

**使用ssh-keygen生成公钥和私钥：**  
	
	#~/.ssh/id_rsa目录下生成公钥和私钥
	ssh-keygen -t rsa -P '' -f ~/.ssh/id_rsa

**将公钥导入到authorized_keys文件中：**

进入~/.ssh/id_rsa目录后发现有id_rsa和id_rsa.pub两个文件，其中id_rsa.pub就是公钥文件了。

> 将A节点（服务器）生成的公钥导入到B节点的authorized_keys文件中后，则A节点在B节点中就是一个合法的主机，当A通过ssh连接到B节点时，就不再需要输入密码了。  
> 由于在伪分布模式下，两个节点都是当前主机，所以直接将刚才生成的公钥导入到~/.ssh/目录下的authorized_keys文件中即可

	cat id_rsa.pub >> ~/.ssh/authorized_keys

由于使用>>重定向生成的authorized_keys新文件，默认权限设置不正确，故需要将该文件的权限修改为600之后才能起作用：

	chmod 600 ~/.ssh/authorized_keys


**测试ssh是否已正常配置：**

	#使用ssh登录本机，若能够直接登录成功而不需要输入密码则表明ssh已正确配置
	ssh localhost		#第一次登录需要输入yes，之后再登录就不需要输入了

	#退出登录
	exit

**格式化hadoop文件系统**
	
	hadoop namenode -format

在格式化hadoop文件系统过程中有可能会失败，报WARN net.DNS: Unable to determine address of the host-falling back to "localhost" address错误，这是由于在Linux的hosts文件中没有将localhost映射到主机名上，可按以下步骤处理：

	#1、首先查看主机名称
	[hqq@study ~]$ hostname
	study.centos.hqq

	#2、查看hosts文件是否有将映射localhost到主机名
	[hqq@study ~]$ cat /etc/hosts
	127.0.0.1   localhost study.centos.hqq		<== 若没有这一行则添加上去
	127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
	::1         localhost localhost.localdomain localhost6 localhost6.localdomain6

	#3、添加好之后重启网络network
	[hqq@study ~]$ /etc/init.d/network restart

	##最后再执行格式化hadoop文件系统命令

**启动hadoop（基于指定配置目录）**

启动hadoop需要启动三个文件：
	
	#1、启动hdfs服务
	[hqq@study ~]$ start-dfs.sh --config $HADOOP_INSTALL/etc/hadoop_pseudo

	#2、启动yarn框架服务
	[hqq@study ~]$ start-yarn.sh --config $HADOOP_INSTALL/etc、hadoop_pseudo

	#3、启动历史作业守护进程服务(待验证)
	[hqq@study ~]$ mr-jobhistory-daemon.sh start historyserver --config $HADOOP_INSTALL/etc/hadoop_pseudo

当然也可以使用start-all.sh命令一次性启动所有服务：

	[hqq@study ~]$ start-all.sh --config $HADOOP_INSTALL/etc/hadoop_pseudo
	

**查看hadoop是否正常启动：**  
查看hadoop是否已正常启动有两种方法：一是使用jps命令查看java进程是否包括有hadoop相关信息；二是通过浏览器访问http://localhost:50070查看namenode状态，http://localhost:8088查看ResourceManager等

	[hqq@study hadoop_pseudo]$ jps
	11860 NameNode
	12533 Jps
	12359 ResourceManager
	12472 NodeManager
	11980 DataNode
	12189 SecondaryNameNode



**停止hadoop**

停止hadoop各个服务时，正好跟启动服务顺序相反（停止服务不需要指定配置目录）：
	
	#1、先停止历史作业守护进程服务（待验证）
	[hqq@study ~]$ mr-jobhistory-daemon.sh stop historyserver

	#2、再停止yarn框架服务
	[hqq@study ~]$ stop-yarn.sh
	
	#3、最后停止hdfs服务
	[hqq@study ~]$ stop-dfs.sh

当然也可以使用stop-all.sh一次性全部停止服务
	
	[hqq@study ~]$ stop-all.sh


**配置HADOOP_CONF_DIR环境变量**
	
	#如果不设置该变量，则在当前独立模式与伪分布模式共存在的情况下，
	#hadoop使用的文件系统是缺省的单机模式下的文件系统（也就是本地的文件系统），
	#因为我们需要hadoop使用的HDFS文件系统，所以需要该变量指向到伪分布模式的配置目录里
	[hqq@study ~]$ export HADOOP_CONF_DIR=$HADOOP_INSTALL/etc/hadoop_pseudo

**查看hadoop文件系统**

	[hqq@study ~]$ hadoop fs -ls /   #一开始时空白的，就是说没有任何目录

	# 说明一下：如果是独立模式下时，则该命令就列出Linux本身的目录结构出来，
	因为在独立模式（即单机模式）下，hadoop默认使用的文件系统就是本地的文件系统。

**在hadoop文件系统中创建目录**
	
	#在hadoop文件系统里的根目录下创建user目录
	[hqq@study ~]$ hadoop fs -mkdir /user/

	#查看创建的目录
	[hqq@study !]$ hadoop fs -ls /
	Found 1 items
	drwxr-xr-x   - hqq supergroup          0 2018-03-03 01:50 /user














