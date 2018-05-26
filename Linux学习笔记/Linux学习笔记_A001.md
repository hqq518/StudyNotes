## Linux学习笔记
2/9/2018 3:42:28 PM 

---------------------------------------------------

查看指令的帮助说明： --help

	[hqq@localhost 桌面]$ date --help


查看指令的详情用法： man page，在man page中，可以输入“/关键字”高亮显示，按空格向下翻页，按q退出man page。 man page显示的数据文件通常放在/usr/share/man文件目录里。

	[hqq@localhost 桌面]$ man date

> 在man page中第一行指令后面跟着一个圆括号，如DATE(1)，括号内的数字表示该指令的使用范围：

> - 1 表示在shell环境中可以操作的指令或可执行文件
- 2 表示系统核心可调用的函数或工具等
- 3 表示一些常用的函数(function)与函数库（library）
- 4 表示设备文件的说明，通常是/dev下的文件
- 5 表示配置文件或某些文件的格式
- 6 表示游戏（games）
- 7 表示惯例与协定等，如Linux文件系统、网络协议等
- 8 表示系统管理员可用的管理指令
- 9 表示跟kernel有关的文件

搜寻特定指令/文件的man page说明文档： man -f 指令或文件名
	
	[hqq@localhost 桌面]$ man -f date

通过关键字搜寻指令/文件的man page说明文档： man -k 指标或文件名关键字

	[hqq@localhost 桌面]$ man -k dat


超简单文本编辑器： nano

> 在Linux系统当中有非常多的文本编辑器存在，其中最重要的就是后续章节我们会谈到的 vim
这家伙！ 不过其实还有很多不错用的文书编辑器存在的！在这里我们就介绍一下简单的nano
这一支文本编辑器来玩玩先！nano的使用其实很简单，你可以直接加上文件名就能够打开一个旧文件或新文件

	[hqq@localhost 桌面]$ nano text.txt

> 进入nano后最底下有几个常用指令说明，指数符号（^）代表的是键盘的[Ctrl]按键啦！下面先来说说比较重要的几个组合按键：

>- [ctrl]-G：取得线上说明（help） ，很有用的！
- [ctrl]-X：离开naon软件，若有修改过文件会提示是否需要储存喔！
- [ctrl]-O：储存盘案，若你有权限的话就能够储存盘案了；
- [ctrl]-R：从其他文件读入数据，可以将某个文件的内容贴在本文件中；
- [ctrl]-W：搜寻字串，这个也是很有帮助的指令喔！
- [ctrl]-C：说明目前光标所在处的行数与列数等信息；
- [ctrl]-_：可以直接输入行号，让光标快速移动到该行；
- [alt]-Y：校正语法功能打开或关闭（按一下开、再按一下关）
- [alt]-M：可以支持鼠标来移动光标的功能


----------

数据同步写入磁盘： sync

> 一般在关机或重启系统之前先手动将内存中的数据同步写入到硬盘当中。

	[hqq@localhost 桌面]$ sync


惯用的关机指令： shutdown

> [root@study ~]# shutdown [-krhc] [时间] [警告讯息] 

选项与参数：

- -k ： 不要真的关机，只是发送警告讯息出去！
- -r ： 在将系统的服务停掉之后就重新开机（常用）
- -h ： 将系统的服务停掉后，立即关机。 （常用）
- -c ： 取消已经在进行的 shutdown 指令内容。

时间 ： 指定系统关机的时间！时间的范例下面会说明。若没有这个项目，则默认 1 分钟后自动进行。
范例：

	[root@study ~]# shutdown -h now
	立刻关机，其中 now 相当于时间为 0 的状态
	[root@study ~]# shutdown -h 20:25
	系统在今天的 20:25 分会关机，若在21:25才下达此指令，则隔天才关机
	[root@study ~]# shutdown -h +10
	系统再过十分钟后自动关机
	[root@study ~]# shutdown -r now
	系统立刻重新开机
	[root@study ~]# shutdown -r +30 'The system will reboot'
	再过三十分钟系统会重新开机，并显示后面的讯息给所有在线上的使用者
	[root@study ~]# shutdown -k now 'This system will reboot'
	仅发出警告信件的参数！系统并不会关机啦！吓唬人！

其他重新开机、关机指令： reboot, halt, poweroff

	[root@study ~]# halt # 系统停止～屏幕可能会保留系统已经停止的讯息！
	[root@study ~]# poweroff # 系统关机，所以没有提供额外的电力，屏幕空白！