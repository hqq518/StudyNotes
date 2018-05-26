# HBase学习笔记 

 3/31/2018 12:19:25 PM 

------------------

到Apache官网下载[Hbase1.2.6](http://mirrors.shu.edu.cn/apache/hbase/1.2.6/)之后，上传到CentOS7中，并解压。 [中文参考文档](http://abloz.com/hbase/book.html)

## HBase单机模式

进入${hbase_home}/hbase-1.2.6/conf/目录，编辑hbase-site.xml文件，在文件中添加如下内容：

	<configuration>
		<property>
		    <name>hbase.rootdir</name>
		    <value>file:///tmp/hbase</value>	<!-- 内容保存到tmp目录下-->
		</property>
	</configuration>

保存后，进入${hbase_home}/hbase-1.2.6/bin/目录，执行以下命令启动hbase单机模式：

	[hqq@hqq1 bin]$ ./start-hbase.sh

用shell连接你的HBase

	[hqq@hqq1 bin]$ ./hbase shell

进入shell之后，可以进行各个对habse的操作，如创建一个名为"tset"的表，并指定列簇为"cf":
	
	# 创建test表，并指定列簇名为cf
	hbase(main):003:0> create 'test', 'cf'
	0 row(s) in 1.2200 seconds

	# 列出hbase中所有的表名
	hbase(main):003:0> list 'table'
	test
	1 row(s) in 0.0550 seconds

	# 向test表中插入三条数据
	hbase(main):004:0> put 'test', 'row1', 'cf:a', 'value1'
	0 row(s) in 0.0560 seconds
	hbase(main):005:0> put 'test', 'row2', 'cf:b', 'value2'
	0 row(s) in 0.0370 seconds
	hbase(main):006:0> put 'test', 'row3', 'cf:c', 'value3'
	0 row(s) in 0.0450 seconds

以上我们分别插入了3行。第一个行key为row1, 列为 cf:a， 值是 value1。HBase中的列是由 列族前缀和列的名字组成的，以冒号间隔。例如这一行的列名就是a。

检查插入情况，Scan这个表，操作如下：

	hbase(main):007:0> scan 'test'
	ROW        COLUMN+CELL
	row1       column=cf:a, timestamp=1288380727188, value=value1
	row2       column=cf:b, timestamp=1288380738440, value=value2
	row3       column=cf:c, timestamp=1288380747365, value=value3
	3 row(s) in 0.0590 seconds

根据row key值查询一行记录：
	
	hbase(main):008:0> get 'test', 'row1'
	COLUMN      CELL
	cf:a        timestamp=1288380727188, value=value1
	1 row(s) in 0.0400 seconds

删除表之前，需要先将该表设置为disable才能删除：
	
	# 将test表设置为disable
	hbase(main):012:0> disable 'test'
	0 row(s) in 1.0930 seconds

	# 删除表
	hbase(main):013:0> drop 'test'
	0 row(s) in 0.0770 seconds

	# 退出shell
	hbase(main):014:0> exit

停止HBase：

	[hqq@hqq1 bin]$ ./stop-hbase.sh 
	stopping hbase...................


## HBase完全分布式模式

> 在完全分布式时，主机规划如下：hqq1作为master主机,hqq4作为master的备份主机（备份主机跟master主机一样需要配置ssh免密登录其他节点主机。），hqq5,hqq6,hqq7作为数据节点主机。 

 
进入${hbase_home}/hbase-1.2.6/conf/目录，编辑hbase-site.xml文件，在文件中添加如下内容：
	
	<configuration>
		<!-- 配置启用分布式开关 -->
		<property>
			<name>hbase.cluster.distributed</name>
			<value>true</value>
		</property>

		<!-- 配置根目录 -->
		<property>
			<name>hbase.rootdir</name>
			<value>hdfs://hqq1:8020/hbase</value>
		</property>

		<!-- 配置zookeeper节点 -->
		<property>
			<name>hbase.zookeeper.quorum</name>
			<value>hqq1,hqq5,hqq6,hqq7</value>
		</property>
		
		<!-- 配置zookeeper各节点的数据目录 -->
		<property>
		    <name>hbase.zookeeper.property.dataDir</name>
		    <value>/tmp/zookeeper</value>
	    </property>
	</configuration>

进入${hbase_home}/hbase-1.2.6/conf/目录，编辑regionservers文件（配置节点数据主机），添加如下主机：

	hqq5
	hqq6
	hqq7

进入${hbase_home}/hbase-1.2.6/conf/目录，创建backup-masters文件（master的备份主机），并在该文件中添加master备份主机hqq1：
	
	# 创建backup-masters文件
	[hqq@hqq1 conf]$ touch backup-masters

	# 编辑文件
	[hqq@hqq1 conf]$ vim backup-masters

	# 添加如下内容：
	hqq4

将${hbase_home}/hbase-1.2.6/发送到各个节点主机/usr/soft/目录中：
	
	# 发送到hqq4
	[hqq@hqq1 ~]$ scp -r /usr/soft/hbase-1.2.6 hqq@hqq4:/usr/soft/

	# 发送到hqq5
	[hqq@hqq1 ~]$ scp -r /usr/soft/hbase-1.2.6 hqq@hqq5:/usr/soft/

	# 发送到hqq6
	[hqq@hqq1 ~]$ scp -r /usr/soft/hbase-1.2.6 hqq@hqq6:/usr/soft/

	# 发送到hqq7
	[hqq@hqq1 ~]$ scp -r /usr/soft/hbase-1.2.6 hqq@hqq7:/usr/soft/

最后先启动hadoop再启动hbase:
	
	# 启动hadoop
	[hqq@hqq1 ~]$ start-all.sh

	# 进入${hbase_home}/hbase-1.2.6/bin后启动hbase
	[hqq@hqq1 bin]$ ./start-hbase.sh
