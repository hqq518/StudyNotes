# hadoop学习笔记

3/7/2018 9:31:36 PM 

-------------------------

## hadoop 配置完全分布式模式

从VMware中将CentOS克隆出四台主机（完全克隆），然后依次将这四台主机的名称修改为hqq1、hqq2、hqq3、hqq4。因为这四台主机是完全克隆出来的，所以配置以及环境都一样，也都安装有hadoop和jdk等。

**网络拓扑结构：**  
hqq1作为名称节点，也是资源管理器节点（namenode不存储数据，只存储目录），hqq2和hqq3作为数据节点（datanode存储具体的数据），而hqq4作为hqq1的辅助名称节点（secondary node, NN）

**修改Linux主机名称：**

使用root账号修改Linux主机名称，保存后重新系统。依次将这四台主机的名称修改为hqq1、hqq2、hqq3、hqq4。

	[root@study ~]$ vim /etc/hostname
	[root@study ~]$ reboot


**修改hosts文件添加ip映射：**  
使用root账号修改hqq1主机的/etc/hosts文件，在该文件最后添加以下四行内容。

	192.168.209.128 hqq1
	192.168.209.129 hqq2
	192.168.209.130 hqq3
	192.168.209.131 hqq4

hqq1、hqq2、hqq3、hqq4的ip是自动获取的，且是依次递增的，就是说在下次开机时，需要依次顺序开机才能继续保持着ip的顺序不变。

为了验证hosts文件是否已起效，可以使用ping命令来ping一下其他三台主机：
	
	[hqq@hqq1 ~]$ ping hqq1		#按Ctrl + c停止发送数据包
	[hqq@hqq1 ~]$ ping hqq2
	[hqq@hqq1 ~]$ ping hqq3
	[hqq@hqq1 ~]$ ping hqq4


**将hosts文件远程复制到其他主机上：**  
在上一步骤中已经在hqq1主机的hosts文件中添加了自己以及其他三台主机的ip映射了，同理也需要以同步的方法将其他三个主机的hosts文件添加相同的内容。但为了减少工作量，可以在hqq1主机上使用远程复制的方法将hqq1主机的hosts文件拷贝给其他三台主机。

	#将hqq1的/etc/hosts文件远程复制到192.168.209.129主机上的/etc/目录下覆盖原有的文件，
	#以root用户身份去登录远程主机
	[root@hqq1 ~]# scp /etc/hosts root@192.168.209.129:/etc/
	The authenticity of host '192.168.209.129 (192.168.209.129)' can't be established.
	ECDSA key fingerprint is SHA256:tLjRGLp7yL/KbTJwN2H27fpOAV2OGfr0OVp8l0cC4tE.
	ECDSA key fingerprint is MD5:0f:42:78:52:db:01:fb:82:ca:61:d8:58:5e:96:42:e1.
	Are you sure you want to continue connecting (yes/no)? yes  <== 这里输入yes确定覆盖
	Warning: Permanently added '192.168.209.129' (ECDSA) to the list of known hosts.
	root@192.168.209.129's password: 							<==这里输入远程主机root账号的密码

	#以同样的方式将远程复制到其他主机上
	[root@hqq1 ~]# scp /etc/hosts root@192.168.209.130:/etc/
	...
	[root@hqq1 ~]# scp /etc/hosts root@192.168.209.131:/etc/
	...
	[root@hqq1 ~]# scp /etc/hosts root@192.168.209.132:/etc/
	...

远程复制完成之后，可以在hqq2、hqq3、hqq4主机上分别使用ssh命令登录hqq1是否成功，成功说明hosts文件配置没问题（第一次登录时需要输入yes确认，后面再登录就不用了）。

	#在hqq2主机上登录hqq1
	[hqq@hqq2 ~]# ssh hqq1

	#在hqq3主机上登录hqq1
	[hqq@hqq3 ~]# ssh hqq1

	#在hqq3主机上登录hqq1
	[hqq@hqq4 ~]# ssh hqq1


**修改hadoop配置文件：**  
在hqq1主机上修改hadoop的配置文件
	
	# 将原来的伪分布模式的hadoop_pseudo目录复制并重命令为hadoop_clutser目录
	[hqq@hqq4 ~]# cp -r hadoop_pseudo hadoop_clutser

	# 进入hadoop_clutser目录中修改core-site.xml文件如下：
	<configuration>
		<property>
		    <name>fs.defaultFS</name>
		    <value>hdfs://hqq1/</value>     <!-- 将原来localhost修改为hqq1节点 -->
		</property>
	</configuration>

	# 修改hdfs-site.xml文件如下：
	<configuration>
		<property>
		   <name>dfs.replication</name>  
		   <value>2</value>    <!-- 将原来的1（伪分布）改为2（数据节点数），默认3 -->
	   	</property>
		<property>
		  <name>dfs.namenode.secondary.http-address</name>
		  <value>hqq4:50090</value>		<!-- 配置辅助名称节点 -->
		</property>

		<property> 
			<name>dfs.name.dir</name> 
			<value>/home/hqq/hadoop/name/</value> <!-- hadoop的name目录路径 --> 
		</property> 
		<property> 
			<name>dfs.data.dir</name> 
			<value>/home/hqq/hadoop/data/</value> <!-- hadoop的data目录路径 -->
		</property>
	</configuration>

	# 修改yarn-site.xml文件如下：
	<configuration>
		<property>
			<name>yarn.resourcemanager.hostname</name>
			<value>hqq1</value>		<!-- 将原来localhost改为hqq1资源管理器节点-->
		</property>
		<property>
			<name>yarn.nodemanager.aux-services</name>
			<value>mapreduce_shuffle</value>
		</property>
	</configuration>

	# 修改slaves文件，添加数据节点主机名称(hqq2和hqq3)，将原来默认有的localhost去掉
	hqq2
	hqq3
	

**远程复制到其他主机：**
在hqq1主机上将修改好的文件通过远程复制到hqq2、hqq3、hqq4主机上。
	
	# hqq1主机上/usr/soft/hadoop-2.7.5/etc/hadoop_clutser/目录整个远程复制到hqq2主机对应的目录中
	[hqq@hqq1 etc]$ scp -r hadoop_clutser hqq@hqq2:/usr/soft/hadoop-2.7.5/etc/

	# 同样复制到hqq3
	[hqq@hqq1 etc]$ scp -r hadoop_clutser hqq@hqq3:/usr/soft/hadoop-2.7.5/etc/

	# 同样复制到hqq4
	[hqq@hqq1 etc]$ scp -r hadoop_clutser hqq@hqq4:/usr/soft/hadoop-2.7.5/etc/


**修改hadoop连接文件指向hadoop_clutser目录：**	
由于启动hadoop时默认加载的/hadoop/etc/hadoop/目录的的配置文件，所以需要创建一个hadoop链接文件指向到hadoop_clutser目录，让启动时以完成分布式模式启动。

	#在hqq2主机上以hqq登录后进入/usr/soft/hadoop-2.7.5/etc/目录
	[hqq@hqq2 etc]$ rm hadoop		#先删除hadoop链接文件
	[hqq@hqq2 etc]$ ln -s hadoop_clutser hadoop	 #再创建hadoop连接文件指向到hadoop_clutser目录

	#在hqq3主机上以hqq登录后进入/usr/soft/hadoop-2.7.5/etc/目录
	[hqq@hqq3 etc]$ rm hadoop		#先删除hadoop链接文件
	[hqq@hqq3 etc]$ ln -s hadoop_clutser hadoop	 #再创建hadoop连接文件指向到hadoop_clutser目录

	#在hqq4主机上以hqq登录后进入/usr/soft/hadoop-2.7.5/etc/目录
	[hqq@hqq4 etc]$ rm hadoop		#先删除hadoop链接文件
	[hqq@hqq4 etc]$ ln -s hadoop_clutser hadoop	 #再创建hadoop连接文件指向到hadoop_clutser目录


**格式化文件系统并启动hadoop:**  

	# 在hqq1主机上执行格式化hadoop文件系统
	[hqq@hqq1 ~]$ hadoop namenode -format
	...

	# 在hqq1主机上启动hadoop
	[hqq@hqq1 ~]$ start-all.sh


**验证hadoop是否启动成功：**  

	#在hqq1主机上查看jps进程，只有资源管理器、名称节点和辅助名称节点
	[hqq@hqq1 etc]$ jps
	7892 Jps
	7613 ResourceManager
	7247 NameNode
	7455 SecondaryNameNode

	# 在hqq1主机上通过ssh登录hqq2并查看进程，只有数据节点和节点管理器
	[hqq@hqq1 etc]$ ssh hqq2 jps
	4039 DataNode
	4141 NodeManager
	4286 Jps

	# 在hqq1主机上通过ssh登录hqq3并查看进程，只有数据节点和节点管理器
	[hqq@hqq1 etc]$ ssh hqq3 jps
	3988 NodeManager
	4133 Jps
	3886 DataNode

	# 在hqq1主机上通过ssh登录hqq4并查看进程，hqq4是作为辅助名称节点，由于当前没有配置，所以没有任何进程 
	[hqq@hqq1 etc]$ ssh hqq4 jps
	3818 Jps

	
	