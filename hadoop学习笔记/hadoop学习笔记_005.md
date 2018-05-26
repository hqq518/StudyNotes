# hadoop学习笔记

3/16/2018 10:17:22 PM 

----------------------

## MapReduce集群模式调整

### 从hadoop的相关jar包中查找所有默认的配置文件：
	
	core-site.xml默认配置：
		hadoop-common-2.7.5.jar/core-default.xml
	
	hdfs-site.xml默认配置：
		hadoop-hdfs-2.7.5.jar/hdfs-default.xml

	mapred-site.xml默认配置：
		hadoop-mapreduce-client-core-2.7.5.jar/mapred-defualt.xml

	yarn-site.xml默认配置：
		hadoop-yarn-common-2.7.5.jar/yarn-default.xml

### 调整hadoop数据节点每个数据块的大小（默认128M），和每个数据块的副本数量（默认3个）
	
	# hdfs-site.xml文件中：
	<!-- 设置每个数据块大小 -->
	<property>
		<name>dfs.blocksize</name>
		<value>20k</value>
	</property>
	<!-- 设置每个数据块的副本数 -->
	<property>
		<name>dfs.replication</name>
		<value>2</value>
	</property>

修改完成并保存后，将该hdfs-site.xml文件远程复制到其他主机上：

1. 停止hadoop集群： stop-all.sh
2. 删除所有节点的临时目录： /tmp/hadoop-hqq
3. 重新格式化文件系统： hadoop namenode -format
4. 启动hadoop集群：start-dfs.sh、start-yarn.sh


### 在Linux命令终端中切换前后台进程：

1. 以后台进程方式启动某个软件： +&
2. 若某个软件已在前台启动，欲将其转为后台运行，则首先使用Ctrl + z暂停程序，然后输入bg + %1 (1是进程编号，可通过jobs命令查看)
3. 若某个软件已在后台启动，欲将其转了前台运行，则输入fg + %1 (1是进程编号，可通过jobs命令查看)