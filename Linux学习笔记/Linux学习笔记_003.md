## Linux学习笔记

2/27/2018 10:20:34 PM 

----------------------------

### RPM 查询软件信息 （ query）

RPM 在查询的时候，其实查询的地方是在 /var/lib/rpm/ 这个目录下的数据库文件啦！另外，
RPM 也可以查询未安装的 RPM 文件内的信息喔！那如何去查询呢？ 我们先来谈谈可用的选
项有哪些？

> [root@study ~]# rpm -qa &lt;==已安装软件  
[root@study ~]# rpm -q[licdR] 已安装的软件名称 &lt;==已安装软件  
[root@study ~]# rpm -qf 存在于系统上面的某个文件名 &lt;==已安装软件 
[root@study ~]# rpm -qp[licdR] 未安装的某个文件名称 &lt;==查阅RPM文件  
选项与参数：  
查询已安装软件的信息： 
-q ：仅查询，后面接的软件名称是否有安装；  
-qa ：列出所有的，已经安装在本机 Linux 系统上面的所有软件名称；  
-qi ：列出该软件的详细信息 （ information） ，包含开发商、版本与说明等；  
-ql ：列出该软件所有的文件与目录所在完整文件名 （ list） ；  
-qc ：列出该软件的所有配置文件 （ 找出在 /etc/ 下面的文件名而已）  
-qd ：列出该软件的所有说明文档 （ 找出与 man 有关的文件而已）  
-qR ：列出与该软件有关的相依软件所含的文件 （ Required 的意思）  
-qf ：由后面接的文件名称，找出该文件属于哪一个已安装的软件；  
-q --scripts：列出是否含有安装后需要执行的脚本档，可用以 debug 喔！  
查询某个 RPM 文件内含有的信息：  
-qp[icdlR]：注意 -qp 后面接的所有参数以上面的说明一致。但用途仅在于找出
某个 RPM 文件内的信息，而非已安装的软件信息！注意！

	范例一：找出你的 Linux 是否有安装 logrotate 这个软件？
	[root@study ~]# rpm -q logrotate
	logrotate-3.8.6-4.el7.x86_64
	[root@study ~]# rpm -q logrotating
	package logrotating is not installed
	# 注意到，系统会去找是否有安装后面接的软件名称。注意，不必要加上版本喔！
	# 至于显示的结果，一看就知道有没有安装啦！

	范例二：列出上题当中，属于该软件所提供的所有目录与文件：
	[root@study ~]# rpm -ql logrotate
	/etc/cron.daily/logrotate
	/etc/logrotate.conf
	....（ 以下省略） ....
	# 可以看出该软件到底提供了多少的文件与目录，也可以追踪软件的数据。

	范例三：列出 logrotate 这个软件的相关说明数据：
	[root@study ~]# rpm -qi logrotate
	Name : logrotate # 软件名称
	Version : 3.8.6 # 软件的版本
	Release : 4.el7 # 释出的版本
	Architecture: x86_64 # 编译时所针对的硬件等级
	Install Date: Mon 04 May 2015 05:52:36 PM CST # 这个软件安装到本系统的时间
	Group : System Environment/Base # 软件是放再哪一个软件群组中
	Size : 102451 # 软件的大小
	License : GPL+ # 释出的授权方式
	Signature : RSA/SHA256, Fri 04 Jul 2014 11:34:56 AM CST, Key ID 24c6a8a7f4a80eb5
	Source RPM : logrotate-3.8.6-4.el7.src.rpm # 这就是 SRPM 的文件名
	Build Date : Tue 10 Jun 2014 05:58:02 AM CST # 软件编译打包的时间
	Build Host : worker1.bsys.centos.org # 在哪一部主机上面编译的
	Relocations : （ not relocatable）
	Packager : CentOS BuildSystem &lt;http://bugs.centos.org&gt;
	Vendor : CentOS
	URL : https://fedorahosted.org/logrotate/
	Summary : Rotates, compresses, removes and mails system log files
	Description : # 这个是详细的描述！
	The logrotate utility is designed to simplify the administration of
	log files on a system which generates a lot of log files. Logrotate
	allows for the automatic rotation compression, removal and mailing of
	log files. Logrotate can be set to handle a log file daily, weekly,
	monthly or when the log file gets to a certain size. Normally,
	logrotate runs as a daily cron job.
	Install the logrotate package if you need a utility to deal with the
	log files on your system.
	# 列出该软件的 information （ 信息） ，里面的信息可多着呢，包括了软件名称、
	# 版本、开发商、SRPM文件名称、打包次数、简单说明信息、软件打包者、
	# 安装日期等等！如果想要详细的知道该软件的数据，用这个参数来了解一下

	范例四：分别仅找出 logrotate 的配置文件与说明文档
	[root@study ~]# rpm -qc logrotate
	[root@study ~]# rpm -qd logrotate

	范例五：若要成功安装 logrotate ，他还需要什么文件的帮忙？
	[root@study ~]# rpm -qR logrotate
	/bin/sh
	config（ logrotate） = 3.8.6-4.el7
	coreutils &gt;= 5.92
	....（ 以下省略） ....
	# 由这里看起来，呵呵～还需要很多文件的支持才行喔！

	范例六：由上面的范例五，找出 /bin/sh 是那个软件提供的？
	[root@study ~]# rpm -qf /bin/sh
	bash-4.2.46-12.el7.x86_64
	# 这个参数后面接的可是“文件”呐！不像前面都是接软件喔！
	# 这个功能在查询系统的某个文件属于哪一个软件所有的。

	范例七：假设我有下载一个 RPM 文件，想要知道该文件的需求文件，该如何？
	[root@study ~]# rpm -qpR filename.i386.rpm
	# 加上 -qpR ，找出该文件需求的数据！

### RPM 验证与数码签章 （ Verify/signature）
当你有数据不小心遗失， 或者是因为你误杀了某个软件的文件，或者是不小心不知道修改到某
一个软件的文件内容， 就用这个简单的方法来验证一下原本的文件系统吧！好让你了解这一
阵子到底是修改到哪些文件数据了！

> [root@study ~]# rpm -Va  
[root@study ~]# rpm -V 已安装的软件名称  
[root@study ~]# rpm -Vp 某个 RPM 文件的文件名  
[root@study ~]# rpm -Vf 在系统上面的某个文件  
选项与参数：  
-V ：后面加的是软件名称，若该软件所含的文件被更动过，才会列出来；  
-Va ：列出目前系统上面所有可能被更动过的文件；  
-Vp ：后面加的是文件名称，列出该软件内可能被更动过的文件；  
-Vf ：列出某个文件是否被更动过～  

	范例一：列出你的 Linux 内的 logrotate 这个软件是否被更动过？
	[root@study ~]# rpm -V logrotate
	# 如果没有出现任何讯息，恭喜你，该软件所提供的文件没有被更动过。
	# 如果有出现任何讯息，才是有出现状况啊！

	范例二：查询一下，你的 /etc/crontab 是否有被更动过？
	[root@study ~]# rpm -Vf /etc/crontab
	.......T. c /etc/crontab
	# 瞧！因为有被更动过，所以会列出被更动过的信息类型！

你会发现在文件名之前有个 c ，然后就是一堆奇怪的文字了。那个 c 代表的是 configuration，就是配置文件的意思。至于最前面的几个信息是：

- S ：（ file Size differs） 文件的容量大小是否被改变  
- M ：（ Mode differs） 文件的类型或文件的属性 （ rwx） 是否被改变？如是否可执行等参数已被改变  
- 5 ：（ MD5 sum differs） MD5 这一种指纹码的内容已经不同
- D ：（ Device major/minor number mis-match） 设备的主/次代码已经改变
- L ：（ readLink（ 2） path mis-match） Link 路径已被改变
- U ：（ User ownership differs） 文件的所属人已被改变
- G ：（ Group ownership differs） 文件的所属群组已被改变
- T ：（ mTime differs） 文件的创建时间已被改变
- P ：（ caPabilities differ） 功能已经被改变

至于那个 c 代表的是“ Config file ”的意思，也就是文件的类型，文件类型有下面这几类：

- c ：配置文件 （ config file）
- d ：文件数据文件 （ documentation）
- g ：鬼文件～通常是该文件不被某个软件所包含，较少发生！（ ghost file）
- l ：授权文件 （ license file）
- r ：读我文件 （ read me）

### RPM 反安装与重建数据库 （ erase/rebuilddb）

反安装就是将软件解除安装（卸载）啦！要注意的是，“解安装的过程一定要由最上层往下解除”。移除的选项很简单，就通过 -e 即可移除。不过，很常发生软件属性相依导致无法移除某些软件的问题！ 我们以下面的例子来说明：

	# 1\. 找出与 pam 有关的软件名称，并尝试移除 pam 这个软件：
	[root@study ~]# rpm -qa | grep pam
	fprintd-pam-0.5.0-4.0.el7_0.x86_64
	pam-1.1.8-12.el7.x86_64
	gnome-keyring-pam-3.8.2-10.el7.x86_64
	pam-devel-1.1.8-12.el7.x86_64
	pam_krb5-2.4.8-4.el7.x86_64

	[root@study ~]# rpm -e pam
	error: Failed dependencies: <==这里提到的是相依性的问题
	libpam.so.0（ ） （ 64bit） is needed by （ installed） systemd-libs-208-20.el7.x86_64
	libpam.so.0（ ） （ 64bit） is needed by （ installed） libpwquality-1.2.3-4.el7.x86_64
	....（ 以下省略） ....

	# 2\. 若仅移除 pam-devel 这个之前范例安装上的软件呢？
	[root@study ~]# rpm -e pam-devel <==不会出现任何讯息！
	[root@study ~]# rpm -q pam-devel
	package pam-devel is not installed


### 利用 yum 进行查询、安装、升级与移除功能

**查询功能：yum [list|info|search|provides|whatprovides] 参数**  

> [root@study ~]# yum [option] [查询工作项目] [相关参数]  
选项与参数：  
[option]：主要的选项，包括有：  
-y ：当 yum 要等待使用者输入时，这个选项可以自动提供 yes 的回应；  
--installroot=/some/path ：将该软件安装在 /some/path 而不使用默认路径  
[查询工作项目] [相关参数]：这方面的参数有：  
search ：搜寻某个软件名称或者是描述 （ description） 的重要关键字；  
list ：列出目前 yum 所管理的所有的软件名称与版本，有点类似 rpm -qa；  
info ：同上，不过有点类似 rpm -qai 的执行结果；  
provides：从文件去搜寻软件！类似 rpm -qf 的功能！   

	范例一：搜寻磁盘阵列（raid）相关的软件有哪些？
	[root@study ~]# yum search raid
	Loaded plugins: fastestmirror, langpacks # yum 系统自己找出最近的 yum server
	Loading mirror speeds from cached hostfile # 找出速度最快的那一部 yum server
	* base: ftp.twaren.net # 下面三个软件库，且来源为该服务器！
	* extras: ftp.twaren.net
	* updates: ftp.twaren.net
	....（ 前面省略） ....
	dmraid-events-logwatch.x86_64 : dmraid logwatch-based email reporting
	dmraid-events.x86_64 : dmevent_tool （ Device-mapper event tool） and DSO
	iprutils.x86_64 : Utilities for the IBM Power Linux RAID adapters
	mdadm.x86_64 : The mdadm program controls Linux md devices （ software RAID arrays）
	....（ 后面省略） ....
	# 在冒号 （ :） 左边的是软件名称，右边的则是在 RPM 内的 name 设置 （ 软件名）

	范例二：找出 mdadm 这个软件的功能为何
	[root@study ~]# yum info mdadm
	Installed Packages <==这说明该软件是已经安装的了
	Name : mdadm <==这个软件的名称
	Arch : x86_64 <==这个软件的编译架构
	Version : 3.3.2 <==此软件的版本
	Release : 2.el7 <==释出的版本
	Size : 920 k <==此软件的文件总容量
	Repo : installed <==软件库回报说已安装的
	From repo : anaconda
	Summary : The mdadm program controls Linux md devices （ software RAID arrays）
	URL : http://www.kernel.org/pub/linux/utils/raid/mdadm/
	License : GPLv2+
	Description : The mdadm program is used to create, manage, and monitor Linux MD （ software
	: RAID） devices. As such, it provides similar functionality to the raidtools
	: package. However, mdadm is a single program, and it can perform
	: almost all functions without a configuration file, though a configuration
	: file can be used to help with some common tasks.
	# 不要跟我说，上面说些啥？自己找字典翻一翻吧！拜托拜托！

	范例三：列出 yum 服务器上面提供的所有软件名称
	[root@study ~]# yum list
	Installed Packages <==已安装软件
	GConf2.x86_64 3.2.6-8.el7 @anaconda
	LibRaw.x86_64 0.14.8-5.el7.20120830git98d925 @base
	ModemManager.x86_64 1.1.0-6.git20130913.el7 @anaconda
	....（ 中间省略） ....
	Available Packages <==还可以安装的其他软件
	389-ds-base.x86_64 1.3.3.1-20.el7_1 updates
	389-ds-base-devel.x86_64 1.3.3.1-20.el7_1 updates
	389-ds-base-libs.x86_64 1.3.3.1-20.el7_1 updates
	....（ 下面省略） ....
	# 上面提供的意义为：“ 软件名称 版本 在那个软件库内 ”

	范例四：列出目前服务器上可供本机进行升级的软件有哪些？
	[root@study ~]# yum list updates <==一定要是 updates 喔！
	Updated Packages
	NetworkManager.x86_64 1:1.0.0-16.git20150121.b4ea599c.el7_1 updates
	NetworkManager-adsl.x86_64 1:1.0.0-16.git20150121.b4ea599c.el7_1 updates
	....（ 下面省略） ....
	# 上面就列出在那个软件库内可以提供升级的软件与版本！

	范例五：列出提供 passwd 这个文件的软件有哪些
	[root@study ~]# yum provides passwd
	passwd-0.79-4.el7.x86_64 : An utility for setting or changing passwords using PAM
	Repo : base
	passwd-0.79-4.el7.x86_64 : An utility for setting or changing passwords using PAM
	Repo : @anaconda
	# 找到啦！就是上面的这个软件提供了 passwd 这个程序！

	范例六：找出以 pam 为开头的软件名称有哪些
	[root@study ~]# yum list pam*
	Installed Packages
	pam.x86_64 1.1.8-12.el7 @anaconda
	pam_krb5.x86_64 2.4.8-4.el7 @base
	Available Packages <==下面则是“可升级”的或“未安装”的
	pam.i686 1.1.8-12.el7_1.1 updates
	pam.x86_64 1.1.8-12.el7_1.1 updates
	pam-devel.i686 1.1.8-12.el7_1.1 updates
	pam-devel.x86_64 1.1.8-12.el7_1.1 updates
	pam_krb5.i686 2.4.8-4.el7 base
	pam_pkcs11.i686 0.6.2-18.el7 base
	pam_pkcs11.x86_64 0.6.2-18.el7 base


**安装/升级功能：yum [install|update] 软件**  

> [root@study ~]# yum [option] [安装与升级的工作项目] [相关参数]  
选项与参数：  
install ：后面接要安装的软件！  
update ：后面接要升级的软件，若要整个系统都升级，就直接 update 即可  

	范例一：将前一个练习找到的未安装的 pam-devel 安装起来
	[root@study ~]# yum install pam-devel
	Loaded plugins: fastestmirror, langpacks # 首先的 5 行在找出最快的 yum server
	Loading mirror speeds from cached hostfile
	* base: ftp.twaren.net
	* extras: ftp.twaren.net
	* updates: ftp.twaren.net
	Resolving Dependencies # 接下来先处理“属性相依”的软件问题
	--> Running transaction check
	---> Package pam-devel.x86_64 0:1.1.8-12.el7_1.1 will be installed
	--> Processing Dependency: pam（ x86-64） = 1.1.8-12.el7_1.1 for package: pam-devel-
	1.1.8-12.el7_1.1.x86_64
	--> Running transaction check
	---> Package pam.x86_64 0:1.1.8-12.el7 will be updated
	---> Package pam.x86_64 0:1.1.8-12.el7_1.1 will be an update
	--> Finished Dependency Resolution
	Dependencies Resolved
	# 由上面的检查发现到 pam 这个软件也需要同步升级，这样才能够安装新版 pam-devel 喔！
	# 至于下面则是一个总结的表格显示！
	==========================================================================================
	Package Arch Version Repository Size
	==========================================================================================
	Installing:
	pam-devel x86_64 1.1.8-12.el7_1.1 updates 183 k
	Updating for dependencies:
	pam x86_64 1.1.8-12.el7_1.1 updates 714 k
	Transaction Summary
	==========================================================================================
	Install 1 Package # 要安装的是一个软件
	Upgrade （ 1 Dependent package） # 因为相依属性问题，需要额外加装一个软件！
	Total size: 897 k
	Total download size: 183 k # 总共需要下载的容量！
	Is this ok [y/d/N]: y # 你得要自己决定是否要下载与安装！当然是 y 啊！
	Downloading packages: # 开始下载啰！
	warning: /var/cache/yum/x86_64/7/updates/packages/pam-devel-1.1.8-12.el7_1.1.x86_64.rpm:
	Header V3 RSA/SHA256 Signature, key ID f4a80eb5: NOKEY
	Public key for pam-devel-1.1.8-12.el7_1.1.x86_64.rpm is not installed
	pam-devel-1.1.8-12.el7_1.1.x86_64.rpm &#124; 183 kB 00:00:00
	Retrieving key from file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
	Importing GPG key 0xF4A80EB5:
	Userid : "CentOS-7 Key （ CentOS 7 Official Signing Key） &lt;security@centos.org&gt;"
	Fingerprint: 6341 ab27 53d7 8a78 a7c2 7bb1 24c6 a8a7 f4a8 0eb5
	Package : centos-release-7-1.1503.el7.centos.2.8.x86_64 （ @anaconda）
	From : /etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
	Is this ok [y/N]: y # 只有在第一次安装才会出现这个项目“确定要安装数码签章”才能继续！
	Running transaction check
	Running transaction test
	Transaction test succeeded
	Running transaction
	Warning: RPMDB altered outside of yum.
	Updating : pam-1.1.8-12.el7_1.1.x86_64 1/3
	Installing : pam-devel-1.1.8-12.el7_1.1.x86_64 2/3
	Cleanup : pam-1.1.8-12.el7.x86_64 3/3
	Verifying : pam-1.1.8-12.el7_1.1.x86_64 1/3
	Verifying : pam-devel-1.1.8-12.el7_1.1.x86_64 2/3
	Verifying : pam-1.1.8-12.el7.x86_64 3/3
	Installed:
	pam-devel.x86_64 0:1.1.8-12.el7_1.1
	Dependency Updated:
	pam.x86_64 0:1.1.8-12.el7_1.1
	Complete!


**移除功能：yum [remove] 软件**  
	[root@study ~]# yum remove pam-devel
	Loaded plugins: fastestmirror, langpacks
	Resolving Dependencies &lt;==同样的，先解决属性相依的问题
	--> Running transaction check
	---> Package pam-devel.x86_64 0:1.1.8-12.el7_1.1 will be erased
	--> Finished Dependency Resolution
	Dependencies Resolved
	==========================================================================================
	Package Arch Version Repository Size
	==========================================================================================
	Removing:
	pam-devel x86_64 1.1.8-12.el7_1.1 @updates 528 k
	Transaction Summary
	==========================================================================================
	Remove 1 Package # 还好！没有相依属性的问题，仅移除一个软件！
	Installed size: 528 k
	Is this ok [y/N]: y
	Downloading packages:
	Running transaction check
	Running transaction test
	Transaction test succeeded
	Running transaction
	Erasing : pam-devel-1.1.8-12.el7_1.1.x86_64 1/1
	Verifying : pam-devel-1.1.8-12.el7_1.1.x86_64 1/1
	Removed:
	pam-devel.x86_64 0:1.1.8-12.el7_1.1
	Complete!

### yum 的配置文件
虽然 yum 是你的主机能够连线上 Internet 就可以直接使用的，不过，由于 CentOS 的映射站台可能会选错，所以有时候将yum自动连接的镜像服务器地址修改为国内。

	[root@study ~]# vim /etc/yum.repos.d/CentOS-Base.repo
	[base]
	name=CentOS-$releasever - Base
	mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=os&infra=$infra
	#baseurl=http://mirror.centos.org/centos/$releasever/os/$basearch/
	gpgcheck=1
	gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7

如上所示，仅列出 base 这个软件库内容而已，其他的软件库内容请自行查阅啰！上面的数据需要注意的是：  

- [base]：代表软件库的名字！中括号一定要存在，里面的名称则可以随意取。但是不能有
两个相同的软件库名称， 否则 yum 会不晓得该到哪里去找软件库相关软件清单文件。  
- name：只是说明一下这个软件库的意义而已，重要性不高！
- mirrorlist=：列出这个软件库可以使用的映射站台，如果不想使用，可以注解到这行；
- baseurl=：这个最重要，因为后面接的就是软件库的实际网址！ mirrorlist 是由 yum 程序
自行去捉映射站台， baseurl 则是指定固定的一个软件库网址！我们刚刚找到的网址放到
这里来啦！
- enable=1：就是让这个软件库被启动。如果不想启动可以使用 enable=0 喔！
- gpgcheck=1：还记得 RPM 的数码签章吗？这就是指定是否需要查阅 RPM 文件内的数
码签章！
- gpgkey=：就是数码签章的公钥档所在位置！使用默认值即可    


		范例一：列出目前 yum server 所使用的软件库有哪些？
		[root@study ~]# yum repolist all
		repo id repo name status
		C7.0.1406-base/x86_64 CentOS-7.0.1406 - Base disabled
		C7.0.1406-centosplus/x86_64 CentOS-7.0.1406 - CentOSPlus disabled
		C7.0.1406-extras/x86_64 CentOS-7.0.1406 - Extras disabled
		C7.0.1406-fasttrack/x86_64 CentOS-7.0.1406 - CentOSPlus disabled
		C7.0.1406-updates/x86_64 CentOS-7.0.1406 - Updates disabled
		base CentOS-7 - Base enabled: 8,652
		base-debuginfo/x86_64 CentOS-7 - Debuginfo disabled
		base-source/7 CentOS-7 - Base Sources disabled
		centosplus/7/x86_64 CentOS-7 - Plus disabled
		centosplus-source/7 CentOS-7 - Plus Sources disabled
		cr/7/x86_64 CentOS-7 - cr disabled
		extras CentOS-7 - Extras enabled: 181
		extras-source/7 CentOS-7 - Extras Sources disabled
		fasttrack/7/x86_64 CentOS-7 - fasttrack disabled
		updates CentOS-7 - Updates enabled: 1,302
		updates-source/7 CentOS-7 - Updates Sources disabled
		repolist: 10,135
		# 上面最右边有写 enabled 才是有启动的！由于 /etc/yum.repos.d/
		# 有多个配置文件，所以你会发现还有其他的软件库存在

有时候修改了网址却没有修改软件库名称 ，可能就会造成本机的清单与
yum 服务器的清单不同步，此时就会出现无法更新的问题了！
那怎么办啊？很简单，就清除掉本机上面的旧数据即可！需要手动处理吗？不需要的， 通过
yum 的 clean 项目来处理即可！

	[root@study ~]# yum clean [packages&#124;headers&#124;all]
	选项与参数：
	packages：将已下载的软件文件删除
	headers ：将下载的软件文件开始删除
	all ：将所有软件库数据都删除！

	范例一：删除已下载过的所有软件库的相关数据 （ 含软件本身与清单）
	[root@study ~]# yum clean all