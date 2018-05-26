## Linux学习笔记

2/10/2018 1:28:21 PM 

-------------------------

查看当前目录下文件列表详情（包括隐藏文件）:

	[hqq@study 桌面]$ ls -al

查看当前目录下文件列表详情（带完整日期及时间信息）:
	
	[root@study 桌面]# ls -l --full-time

改变文件或目录所属的群组： chgrp

> [root@study ~]# chgrp [-R] dirname/filename ...  
选项与参数：  
-R : 进行递回（ recursive） 的持续变更，亦即连同次目录下的所有文件、目录
都更新成为这个群组之意。常常用在变更某一目录内所有的文件之情况。

	[root@study 桌面]# chgrp root text.txt


改变文件或目录的拥有者： chown

> chown的用途还满多的，他还可以顺便直接修改群组的名称呢！此外，如果要连目录下的所有
次目录或文件同时更改文件拥有者的话，直接加上 -R 的选项即可！

	[root@study 桌面]# chown root text.txt        //将text.txt文件的拥有者修改为root
	[root@study 桌面]# chown root:root text.txt   //将text.txt文件的拥有者修改为root，同时也该文件的所属群组修改为root(群名)

改变文件或目录的权限：chmod

> 数字类型改变文件权限  
Linux文件的基本权限就有九个，分别是owner/group/others三种身份各有自己的
read/write/execute权限：文件的权限字符为：“-
rwxrwxrwx”， 这九个权限是三个三个一组的！其中，我们可以使用数字来代表各个权
限，各权限的分数对照表如下：  
r：4 > w：2 > x：1  
每种身份（ owner/group/others） 各自的三个权限（ r/w/x） 分数是需要累加的，例如当权
限为： [-rwxrwx---] 分数则是：  
owner = rwx = 4+2+1 = 7 > group = rwx = 4+2+1 = 7 > others= --- = 0+0+0 = 0  
所以该文件的权限数字就是770啦，
chmod的语法是这样的：  
[root@study ~]# chmod [-R] xyz 文件或目录  
选项与参数：  
xyz： 就是刚刚提到的数字类型的权限属性，为 rwx 属性数值的相加。
-R： 进行递回（ recursive） 的持续变更，亦即连同次目录下的所有文件都会变更

	[root@study 桌面]# chmod 770 text.txt


> 符号类型改变文件权限  
还有一个改变权限的方法呦！从之前的介绍中我们可以发现，基本上就九个权限分别是
（1）user （2）group （3）others三种身份啦！那么我们就可以借由u、g、o来代表三种
身份的权限！此外， a 则代表 all 亦即全部的身份！那么读写的权限就可以写成r, w, x啰！也就是可以使用下面的方式来看：  
| chmod | u g o a | +（ 加入） -（ 除去） =（ 设置） | r w x | 文件或目录 |

	//将text.txt文件的权限改为：user=rwx，group和others=r
	[root@study 桌面]# chmod u=rwx,go=r text.txt

> 如果是要将权限去掉而不更动其他已存在的权限呢？使用“a+权限”或“a-权限”。例如要拿掉全部人的可执行权限，则：

	[root@study 桌面]# chmod a-x text.txt




