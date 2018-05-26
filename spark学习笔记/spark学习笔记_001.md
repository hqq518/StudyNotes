# spark学习笔记

5/12/2018 10:25:51 PM 

### 配置环境变量

首页从apache官网将spark最新版本下载到本地，然后上传到虚拟机并解压后，将spark的安装目录复制，以root用户打开/etc/environment 文档，添加以下内容：

	AVA_HOME=/usr/soft/jdk1.8.0_161
	SPARK_HOME=/usr/soft/spark-2.3.0-bin-hadoop2.7
	PATH=/usr/local/bin:/usr/local/sbin:/usr/bin:/usr/sbin:/bin:/sbin:/usr/soft/jdk1.8.0_161/bin:/usr/soft/spark-2.3.0-bin-hadoop2.7/bin

刷新/etc/environment文件，使其生效（或重启系统reboot）:

	[hqq@hqq1 ~]$ source /etc/environment 

### 验证spark是否安装成功（以本地模式启动）
通过启动spark-shell软件查看spark是否已安装成功，其中spark-shell 后需要接一个参数，表示从哪里提取程序运行（这里从本地local）,中括号内的数字表示启动多少个线程，一般与物理机cpu核心数一致。启动后显示如下信息，则表示spark已安装成功。

	[hqq@hqq1 ~]$ spark-shell --master local[4]
	2018-05-12 22:44:10 WARN  NativeCodeLoader:62 - Unable to load native-hadoop library for your platform... using builtin-java classes where applicable
	Setting default log level to "WARN".
	To adjust logging level use sc.setLogLevel(newLevel). For SparkR, use setLogLevel(newLevel).
	Spark context Web UI available at http://hqq1:4040
	Spark context available as 'sc' (master = local[*], app id = local-1526136299144).
	Spark session available as 'spark'.
	Welcome to
	      ____              __
	     / __/__  ___ _____/ /__
	    _\ \/ _ \/ _ `/ __/  '_/
	   /___/ .__/\_,_/_/ /_/\_\   version 2.3.0
	      /_/
	         
	Using Scala version 2.11.8 (Java HotSpot(TM) 64-Bit Server VM, Java 1.8.0_161)
	Type in expressions to have them evaluated.
	Type :help for more information.
	
	scala> 

### 在scala环境下统计文本单词
> sc是scala context（上下文）内置对象，该对象有很多方法（输入sc.之后按tab键可查看所方法），

	# 加载spark_demo.txt文件并赋值给file变量
	scala> val file = sc.textFile("/home/hqq/spark_demo.txt")
	file: org.apache.spark.rdd.RDD[String] = /home/hqq/spark_demo.txt MapPartitionsRDD[1] at textFile at <console>:24
	
	# 统计spark_demo.txt文件有多少行
	scala> file.count
	res1: Long = 4

	# 提取第一行内容
	scala> file.first()
	res2: String = hello, spark

	# 通过过滤器，提取每一行中包含有“spark”单词的行，并返回新的集合
	scala> file.filter(l=>l.contains("spark"))
	res3: org.apache.spark.rdd.RDD[String] = MapPartitionsRDD[2] at filter at <console>:26
	# 统计新的集合的行数
	scala> res3.count
	res4: Long = 2

	# map： 将文件中每一行按空格分隔形成数组，并取每一行数组的大小作为值，并返回一个新集合
	scala> val fileMap = file.map(l=>l.split(" ").size)
	fileMap: org.apache.spark.rdd.RDD[Int] = MapPartitionsRDD[4] at map at <console>:25

	# reduce： 将上面map返回的新集合遍历（reduce），两两比较，返回较大的数字
	scala> fileMap.reduce((a,b)=> if(a > b) a else b)
	res6: Int = 6

	# 上面map和reduce阶段可合成一行来写
	scala> file.map(l=>l.split(" ").size).reduce((a,b)=> if(a > b) a else b)
	res5: Int = 6

	# 也可以使用Math.max()函数快速获取最大值
	scala> file.map(l=> l.split(" ").size).reduce((a,b)=> Math.max(a,b))
	res7: Int = 6

	# 退出scala环境
	scala> :quit


	### 其他方法 ###
	# 备份文件，这个命令会将加载文件到所有的work节点的目录当中
	scala> sc.addFile("/home/hqq/spark_demo.txt")

### 通过web查看spak作业信息
>spark启动后，可以通过web端查看运行的作业等信息: http://ip:4040
	




