## Linux学习笔记

2/12/2018 9:19:05 AM 

--------------------

### 文件与目录管理

**文件与目录的检视： ls**

> 
[root@study ~]# ls [-aAdfFhilnrRSt] 文件名或目录名称..  
[root@study ~]# ls [--color={never,auto,always}] 文件名或目录名称..  
[root@study ~]# ls [--full-time] 文件名或目录名称..  
选项与参数：  
-a ：全部的文件，连同隐藏文件（ 开头为 . 的文件） 一起列出来（常用）  
-A ：全部的文件，连同隐藏文件，但不包括 . 与 .. 这两个目录  
-d ：仅列出目录本身，而不是列出目录内的文件数据（常用）  
-f ：直接列出结果，而不进行排序 （ls 默认会以文件名排序！）  
-F ：根据文件、目录等信息，给予附加数据结构，例如：
*:代表可可执行文件； /:代表目录； =:代表 socket 文件； &#124;:代表 FIFO 文件；
-h ：将文件大小以人类较易读的方式（例如 GB, KB 等等） 列出来；  
-i ：列出 inode 号码，inode 的意义下一章将会介绍；  
-l ：长数据串行出，包含文件的属性与权限等等数据；（常用）  
-n ：列出 UID 与 GID 而非使用者与群组的名称 （UID与GID会在帐号管理提到！）  
-r ：将排序结果反向输出，例如：原本文件名由小到大，反向则为由大到小；  
-R ：连同子目录内容一起列出来，等于该目录下的所有文件都会显示出来；  
-S ：以文件大小大小排序，而不是用文件名排序；  
-t ：依时间排序，而不是用文件名。  
--color=never ：不要依据文件特性给予颜色显示；  
--color=always ：显示颜色  
--color=auto ：让系统自行依据设置来判断是否给予颜色  
--full-time ：以完整时间模式 （包含年、月、日、时、分） 输出  
--time={atime,ctime} ：输出 access 时间或改变权限属性时间 （ctime）
而非内容变更时间 （modification time）

	范例一：将主文件夹下的所有文件列出来（含属性与隐藏文件）
	[root@study ~]# ls -al ~

	范例二：承上题，不显示颜色，但在文件名末显示出该文件名代表的类型（type）
	[root@study ~]# ls -alF --color=never ~

	范例三：完整的呈现文件的修改时间 （modification time）
	[root@study ~]# ls -al --full-time ~


**复制文件或目录： cp**

> 
[root@study ~]# cp [-adfilprsu] 来源文件（source） 目标文件（destination）  
[root@study ~]# cp [options] source1 source2 source3 .... directory  
选项与参数：  
-a ：相当于 -dr --preserve=all 的意思，至于 dr 请参考下列说明；（常用）  
-d ：若来源文件为链接文件的属性（link file） ，则复制链接文件属性而非文件本身；  
-f ：为强制（force） 的意思，若目标文件已经存在且无法打开，则移除后再尝试一次；  
-i ：若目标文件（destination） 已经存在时，在覆盖时会先询问动作的进行（常用）  
-l ：进行硬式链接（hard link） 的链接文件创建，而非复制文件本身；  
-p ：连同文件的属性（权限、用户、时间） 一起复制过去，而非使用默认属性（备份常用） ；  
-r ：递回持续复制，用于目录的复制行为；（常用）  
-s ：复制成为符号链接文件 （symbolic link） ，亦即“捷径”文件；  
-u ：destination 比 source 旧才更新 destination，或 destination 不存在的情况下才复制。  
--preserve=all ：除了 -p 的权限相关参数外，还加入 SELinux 的属性, links, xattr 等也复制了。  
最后需要注意的，如果来源文件有两个以上，则最后一个目的文件一定要是“目录”才行！

	范例一：用root身份，将主文件夹下的 .bashrc 复制到 /tmp 下，并更名为 bashrc
	[root@study ~]# cp ~/.bashrc /tmp/bashrc
	[root@study ~]# cp -i ~/.bashrc /tmp/bashrc
	cp: overwrite `/tmp/bashrc'? n &lt;==n不覆盖，y为覆盖
	# 重复作两次动作，由于 /tmp 下面已经存在 bashrc 了，加上 -i 选项后，
	# 则在覆盖前会询问使用者是否确定！可以按下 n 或者 y 来二次确认呢！
	范例二：变换目录到/tmp，并将/var/log/wtmp复制到/tmp且观察属性：
	[root@study ~]# cd /tmp
	[root@study tmp]# cp /var/log/wtmp . &lt;==想要复制到目前的目录，最后的 . 不要忘
	[root@study tmp]# ls -l /var/log/wtmp wtmp
	-rw-rw-r--. 1 root utmp 28416 Jun 11 18:56 /var/log/wtmp
	-rw-r--r--. 1 root root 28416 Jun 11 19:01 wtmp
	# 注意上面的特殊字体，在不加任何选项的情况下，文件的某些属性/权限会改变；
	# 这是个很重要的特性！要注意喔！还有，连文件创建的时间也不一样了！
	# 那如果你想要将文件的所有特性都一起复制过来该怎办？可以加上 -a 喔！如下所示：
	[root@study tmp]# cp -a /var/log/wtmp wtmp_2
	[root@study tmp]# ls -l /var/log/wtmp wtmp_2
	-rw-rw-r--. 1 root utmp 28416 Jun 11 18:56 /var/log/wtmp
	-rw-rw-r--. 1 root utmp 28416 Jun 11 18:56 wtmp_2
	# 瞭了吧！整个数据特性完全一模一样ㄟ！真是不赖～这就是 -a 的特性！

复制目录：

	范例三：复制 /etc/ 这个目录下的所有内容到 /tmp 下面
	[root@study tmp]# cp /etc/ /tmp
	cp: omitting directory `/etc' &lt;== 如果是目录则不能直接复制，要加上 -r 的选项
	[root@study tmp]# cp -r /etc/ /tmp
	# 还是要再次的强调喔！ -r 是可以复制目录，但是，文件与目录的权限可能会被改变
	# 所以，也可以利用“ cp -a /etc /tmp ”来下达指令喔！尤其是在备份的情况下！


创建链接文件：

> 使用 -l 及 -s 都会创建所谓的链接文件（link file） ，但是这两种链接文件却
有不一样的情况。那个-l 就是所谓的实体链接（hard link），至于-s 则是符号链接（symbolic link）， 简单来说，bashrc_slink 是一个“捷径”，这个捷径会链接到bashrc去！所以你会看到文件名右侧会有个指向（->） 的符号！

	范例四：将范例一复制的 bashrc 创建一个链接文件 （symbolic link）
	[root@study tmp]# ls -l bashrc
	-rw-r--r--. 1 root root 176 Jun 11 19:01 bashrc &lt;==先观察一下文件情况
	[root@study tmp]# cp -s bashrc bashrc_slink
	[root@study tmp]# cp -l bashrc bashrc_hlink
	[root@study tmp]# ls -l bashrc*
	-rw-r--r--. 2 root root 176 Jun 11 19:01 bashrc &lt;==与原始文件不太一样了！
	-rw-r--r--. 2 root root 176 Jun 11 19:01 bashrc_hlink
	lrwxrwxrwx. 1 root root 6 Jun 11 19:06 bashrc_slink -&gt; bashrc


复制链接文件的两种情况：

	范例六：将范例四造成的 bashrc_slink 复制成为 bashrc_slink_1 与bashrc_slink_2
	[root@study tmp]# cp bashrc_slink bashrc_slink_1
	[root@study tmp]# cp -d bashrc_slink bashrc_slink_2
	[root@study tmp]# ls -l bashrc bashrc_slink*
	-rw-r--r--. 2 root root 176 Jun 11 19:01 bashrc
	lrwxrwxrwx. 1 root root 6 Jun 11 19:06 bashrc_slink -&gt; bashrc
	-rw-r--r--. 1 root root 176 Jun 11 19:09 bashrc_slink_1 &lt;==与原始文件相同
	lrwxrwxrwx. 1 root root 6 Jun 11 19:10 bashrc_slink_2 -&gt; bashrc &lt;==是链接文件！
	# 这个例子也是很有趣喔！原本复制的是链接文件，但是却将链接文件的实际文件复制过来了
	# 也就是说，如果没有加上任何选项时，cp复制的是原始文件，而非链接文件的属性！
	# 若要复制链接文件的属性，就得要使用 -d 的选项了！如 bashrc_slink_2 所示。


同时复制多个文件到目录下：

	范例七：将主文件夹的 .bashrc 及 .bash_history 复制到 /tmp 下面
	[root@study tmp]# cp ~/.bashrc ~/.bash_history /tmp
	# 可以将多个数据一次复制到同一个目录去！最后面一定是目录！


总之，由于 cp 有种种的文件属性与权限的特性，所以，在复制时，你必须要清楚的了解到：

- 是否需要完整的保留来源文件的信息？ 保留原有信息 -a
- 来源文件是否为链接文件 （symbolic link file）？ 复制链接文件本身 -d
- 来源文件是否为特殊的文件，例如 FIFO, socket 等？
- 来源文件是否为目录？ 复制目录 -r 或 -a（保留原有信息）


**移除文件或目录： rm**

> 
[root@study ~]# rm [-fir] 文件或目录  
选项与参数：  
-f ：就是 force 的意思，忽略不存在的文件，不会出现警告讯息；  
-i ：互动模式，在删除前会询问使用者是否动作  
-r ：递回删除啊！最常用在目录的删除了！这是非常危险的选项！！！

	范例一：将刚刚在 cp 的范例中创建的 bashrc 删除掉！
	[root@study ~]# cd /tmp
	[root@study tmp]# rm -i bashrc
	rm: remove regular file `bashrc'? y
	# 如果加上 -i 的选项就会主动询问喔，避免你删除到错误的文件名！

	范例二：通过万用字符*的帮忙，将/tmp下面开头为bashrc的文件名通通删除：
	[root@study tmp]# rm -i bashrc*
	# 注意那个星号，代表的是 0 到无穷多个任意字符喔！很好用的东西！

	范例三：将 cp 范例中所创建的 /tmp/etc/ 这个目录删除掉！
	[root@study tmp]# rmdir /tmp/etc
	rmdir: failed to remove '/tmp/etc': Directory not empty &lt;== 删不掉啊！因为这不是空的目录！
	[root@study tmp]# rm -r /tmp/etc
	rm: descend into directory `/tmp/etc'? y
	rm: remove regular file `/tmp/etc/fstab'? y
	rm: remove regular empty file `/tmp/etc/crypttab'? ^C &lt;== 按下 [crtl]+c 中断
	.....（中间省略） .....
	# 因为身份是 root ，默认已经加入了 -i 的选项，所以你要一直按 y 才会删除！
	# 如果不想要继续按 y ，可以按下“ [ctrl]-c ”来结束 rm 的工作。
	# 这是一种保护的动作，如果确定要删除掉此目录而不要询问，可以这样做：
	[root@study tmp]# \rm -r /tmp/etc
	# 在指令前加上反斜线，可以忽略掉 alias 的指定选项喔！至于 alias 我们在bash再谈！
	# 拜托！这个范例很可怕！你不要删错了！删除 /etc 系统是会挂掉的！


**移动文件/目录或更名： mv**

> 
[root@study ~]# mv [-fiu] source destination  
[root@study ~]# mv [options] source1 source2 source3 .... directory  
选项与参数：  
-f ：force 强制的意思，如果目标文件已经存在，不会询问而直接覆盖；  
-i ：若目标文件 （destination） 已经存在时，就会询问是否覆盖！  
-u ：若目标文件已经存在，且 source 比较新，才会更新 （update）  

	范例一：复制一文件，创建一目录，将文件移动到目录中
	[root@study ~]# cd /tmp
	[root@study tmp]# cp ~/.bashrc bashrc
	[root@study tmp]# mkdir mvtest
	[root@study tmp]# mv bashrc mvtest
	# 将某个文件移动到某个目录去，就是这样做！

	范例二：将刚刚的目录名称更名为 mvtest2
	[root@study tmp]# mv mvtest mvtest2 &lt;== 这样就更名了！简单～
	# 其实在 Linux 下面还有个有趣的指令，名称为 rename ，
	# 该指令专职进行多个文件名的同时更名，并非针对单一文件名变更，与mv不同。请man rename。

	范例三：再创建两个文件，再全部移动到 /tmp/mvtest2 当中
	[root@study tmp]# cp ~/.bashrc bashrc1
	[root@study tmp]# cp ~/.bashrc bashrc2
	[root@study tmp]# mv bashrc1 bashrc2 mvtest2
	# 注意到这边，如果有多个来源文件或目录，则最后一个目标文件一定是“目录！”
	# 意思是说，将所有的数据移动到该目录的意思！


### 文件内容查阅

- cat 由第一行开始显示文件内容
- tac 从最后一行开始显示，可以看出 tac 是 cat 的倒着写！
- nl 显示的时候，顺道输出行号！
- more 一页一页的显示文件内容
- less 与 more 类似，但是比 more 更好的是，他可以往前翻页！
- head 只看头几行
- tail 只看尾巴几行
- od 以二进制的方式读取文件内容！

**直接检视文件内容：cat （concatenate）**

> [root@study ~]# cat [-AbEnTv]  
选项与参数：  
-A ：相当于 -vET 的整合选项，可列出一些特殊字符而不是空白而已；  
-b ：列出行号，仅针对非空白行做行号显示，空白行不标行号！  
-E ：将结尾的断行字符 $ 显示出来；  
-n ：打印出行号，连同空白行也会有行号，与 -b 的选项不同；  
-T ：将 [tab] 按键以 ^I 显示出来；  
-v ：列出一些看不出来的特殊字符  

	范例一：检阅 /etc/issue 这个文件的内容
	[root@study ~]# cat /etc/issue
	\S
	Kernel \r on an \m

	范例二：承上题，如果还要加印行号呢？
	[root@study ~]# cat -n /etc/issue
	1 \S
	2 Kernel \r on an \m
	3
	# 所以这个文件有三行！看到了吧！可以印出行号呢！这对于大文件要找某个特定的行时，有点用处！
	# 如果不想要编排空白行的行号，可以使用“cat -b /etc/issue”，自己测试看看：


**直接检视文件内容：tac （从文件结尾倒着显示）**

	[root@study ~]# tac /etc/issue
	Kernel \r on an \m
	\S
	# 嘿嘿！与刚刚上面的范例一比较，是由最后一行先显示喔！


**添加行号打印： nl**

nl 可以将输出的文件内容自动的加上行号！其默认的结果与 cat -n 有点不太一样， nl 可以将行号做比较多的显示设计，包括位数与是否自动补齐 0 等等的功能呢。
 
> [root@study ~]# nl [-bnw] 文件  
选项与参数：  
-b ：指定行号指定的方式，主要有两种：  
-b a ：表示不论是否为空行，也同样列出行号（类似 cat -n） ；  
-b t ：如果有空行，空的那一行不要列出行号（默认值） ；  
-n ：列出行号表示的方法，主要有三种：  
-n ln ：行号在屏幕的最左方显示；  
-n rn ：行号在自己字段的最右方显示，且不加 0 ；  
-n rz ：行号在自己字段的最右方显示，且加 0 ；  
-w ：行号字段的占用的字符数。  

	范例一：用 nl 列出 /etc/issue 的内容
	[root@study ~]# nl /etc/issue
	1 \S
	2 Kernel \r on an \m
	# 注意看，这个文件其实有三行，第三行为空白（没有任何字符） ，
	# 因为他是空白行，所以 nl 不会加上行号喔！如果确定要加上行号，可以这样做：
	[root@study ~]# nl -b a /etc/issue
	1 \S
	2 Kernel \r on an \m
	3
	# 呵呵！行号加上来啰～那么如果要让行号前面自动补上 0 呢？可这样
	[root@study ~]# nl -b a -n rz /etc/issue
	000001 \S
	000002 Kernel \r on an \m
	000003
	# 嘿嘿！自动在自己字段的地方补上 0 了～默认字段是六位数，如果想要改成 3 位数？
	[root@study ~]# nl -b a -n rz -w 3 /etc/issue
	001 \S
	002 Kernel \r on an \m
	003
	# 变成仅有 3 位数啰～


**一页一页翻动： more**

> 在 more 这个程序的运行过程中，你有几个按键可以按的：  
空白键 （space） ：代表向下翻一页；  
Enter ：代表向下翻“一行”；  
/字串 ：代表在这个显示的内容当中，向下搜寻“字串”这个关键字；  
:f ：立刻显示出文件名以及目前显示的行数；  
q ：代表立刻离开 more ，不再显示该文件内容。  
b 或 [ctrl]-b ：代表往回翻页，不过这动作只对文件有用，对管线无用  

	[root@study ~]# more /etc/man_db.conf
	###
	This file is used by the man-db package to configure the man and cat paths.
	# It is also used to provide a manpath for those without one by examining
	# their PATH environment variable. For details see the manpath（5） man page.
	#.
	....（中间省略） .....
	--More--（28%） &lt;== 重点在这一行喔！你的光标也会在这里等待你的指令


**一页一页翻动（可向前后翻页）： less**

使用了 less 时，可以使用 [pageup] [pagedown] 等按键的功能来往前往后翻看文件。

>
空白键 ：向下翻动一页；  
[pageup] ：向上翻动一页；   
/字串 ：向下搜寻“字串”的功能；  
?字串 ：向上搜寻“字串”的功能；  
n ：重复前一个搜寻 （与 / 或 ? 有关！）  
N ：反向的重复前一个搜寻 （与 / 或 ? 有关！）  
g ：前进到这个数据的第一行去；  
G ：前进到这个数据的最后一行去 （注意大小写） ；  
q ：离开 less 这个程序；  

	[root@study ~]# less /etc/man_db.conf
	###
	This file is used by the man-db package to configure the man and cat paths.
	# It is also used to provide a manpath for those without one by examining
	# their PATH environment variable. For details see the manpath（5） man page.
	#.
	....（中间省略） .....
	: &lt;== 这里可以等待你输入指令！


**显示文件前几行： head**

> [root@study ~]# head [-n number] 文件  
选项与参数：  
-n ：后面接数字，代表显示几行的意思  

	[root@study ~]# head /etc/man_db.conf
	# 默认的情况中，显示前面十行！若要显示前 20 行，就得要这样：
	[root@study ~]# head -n 20 /etc/man_db.conf

	范例：如果后面100行的数据都不打印，只打印/etc/man_db.conf的前面几行，该如何是好？
	[root@study ~]# head -n -100 /etc/man_db.conf


**显示文件后几行： tail**

> [root@study ~]# tail [-n number] 文件  
选项与参数：  
-n ：后面接数字，代表显示几行的意思  
-f ：表示持续侦测后面所接的文件名，要等到按下[ctrl]-c才会结束tail的侦测  

	[root@study ~]# tail /etc/man_db.conf
	# 默认的情况中，显示最后的十行！若要显示最后的 20 行，就得要这样：
	[root@study ~]# tail -n 20 /etc/man_db.conf

	范例一：如果不知道/etc/man_db.conf有几行，却只想列出100行以后的数据时？
	[root@study ~]# tail -n +100 /etc/man_db.conf

	范例二：持续侦测/var/log/messages的内容
	[root@study ~]# tail -f /var/log/messages
	&lt;==要等到输入[crtl]-c之后才会离开tail这个指令的侦测！


> 例题：假如我想要显示 /etc/man_db.conf 的第 11 到第 20 行呢？答：这个应该不算难，想一想，在第 11 到第 20 行，那么我取前 20 行，再取后十行，所以结果就是：“ head -n 20 /etc/man_db.conf | tail -n 10 ”，这样就可以得到第 11 到第 20 行之间的内容了！
这两个指令中间有个管线 （|） 的符号存在，这个管线的意思是：“前面的指令所输出的讯息，请通过管线交由后续的指令继续使用”的意思。 所以， head -n 20 /etc/man_db.conf 会将
文件内的 20 行取出来，但不输出到屏幕上，而是转交给后续的 tail 指令继续处理。 因此 tail“不需要接文件名”，因为 tail 所需要的数据是来自于 head 处理后的结果！

	[root@localhost ~]# head -n 20 /etc/man_db.conf | tail -n 10

> 例题：承上一题，那如果我想要列出正确的行号呢？就是屏幕上仅列出 /etc/man_db.conf 的第 11 到第 20 行，且有行号存在？答：我们可以通过 cat -n 来带出行号，然后再通过head/tail 来撷取数据即可！所以就变成如下的模样了： cat -n /etc/man_db.conf | head -n 20 | tail -n 10

	[root@localhost ~]# cat -n /etc/man_db.conf | head -n 20 | tail -n 10


**修改文件时间或创建新文件： touch**

> [root@study ~]# touch [-acdmt] 文件  
选项与参数：  
-a ：仅修订 access time；  
-c ：仅修改文件的时间，若该文件不存在则不创建新文件；  
-d ：后面可以接欲修订的日期而不用目前的日期，也可以使用 --date="日期或时间"  
-m ：仅修改 mtime ；  
-t ：后面可以接欲修订的时间而不用目前的时间，格式为[YYYYMMDDhhmm]  

	范例一：新建一个空的文件并观察时间
	[dmtsai@study ~]# cd /tmp
	[dmtsai@study tmp]# touch testtouch
	[dmtsai@study tmp]# ls -l testtouch
	-rw-rw-r--. 1 dmtsai dmtsai 0 Jun 16 00:45 testtouch
	# 注意到，这个文件的大小是 0 呢！在默认的状态下，如果 touch 后面有接文件，
	# 则该文件的三个时间 （ atime/ctime/mtime） 都会更新为目前的时间。若该文件不存在，
	# 则会主动的创建一个新的空的文件喔！例如上面这个例子！

修改文件时间：
> 通过 touch 这个指令，我们可以轻易的修订文件的日期与时间。并且也可以创建一个空的文件喔！ 不过，要注意的是，即使我们复制一个文件时，复制所有的属性，但也没有办法复制
ctime 这个属性的。 ctime 可以记录这个文件最近的状态 （ status） 被改变的时间。

	范例三：修改案例二的 bashrc 文件，将日期调整为两天前
	[dmtsai@study tmp]# touch -d "2 days ago" bashrc
	[dmtsai@study tmp]# date; ll bashrc; ll --time=atime bashrc; ll --time=ctime bashrc
	Tue Jun 16 00:51:52 CST 2015
	-rw-r--r--. 1 dmtsai dmtsai 231 Jun 14 00:51 bashrc
	-rw-r--r--. 1 dmtsai dmtsai 231 Jun 14 00:51 bashrc
	-rw-r--r--. 1 dmtsai dmtsai 231 Jun 16 00:51 bashrc
	# 跟上个范例比较看看，本来是 16 日变成 14 日了 （ atime/mtime） ～不过， ctime 并没有跟着改变喔！

	范例四：将上个范例的 bashrc 日期改为 2014/06/15 2:02
	[dmtsai@study tmp]# touch -t 201406150202 bashrc
	[dmtsai@study tmp]# date; ll bashrc; ll --time=atime bashrc; ll --time=ctime bashrc
	Tue Jun 16 00:54:07 CST 2015
	-rw-r--r--. 1 dmtsai dmtsai 231 Jun 15 2014 bashrc
	-rw-r--r--. 1 dmtsai dmtsai 231 Jun 15 2014 bashrc
	-rw-r--r--. 1 dmtsai dmtsai 231 Jun 16 00:54 bashrc
	# 注意看看，日期在 atime 与 mtime 都改变了，但是 ctime 则是记录目前的时间！