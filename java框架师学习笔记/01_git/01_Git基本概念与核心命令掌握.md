# Git基本概念与核心命令掌握

8/1/2018 9:09:39 PM 

git官方客户端：[https://git-scm.com/downloads](https://git-scm.com/downloads)

**初始化本地仓库: git init <仓库名>**
	
	# 在当前Java-VIP课程练习/01-GIT目录下初始化一个本地仓库，仓库名hqq，初始化之后会在当前目录下自动创建一个名为hqq的目录
	HUANGQIQIN@HQQ-DELL MINGW64 /g/Java-VIP课程练习/01-GIT
	$ git init hqq

**查看当前仓库状态: git status**

	HUANGQIQIN@HQQ-DELL MINGW64 /g/Java-VIP课程练习/01-GIT/hqq (master)
	$ git status
	On branch master
	
	No commits yet
	
	Untracked files:
	  (use "git add <file>..." to include in what will be committed)
	
	        README.md        <=== 红色文件表示新增且提交的文件
	
	nothing added to commit but untracked files present (use "git add" to track)


**将新增的文件添加到本地暂存区: git add [-A/文件名]**

	# 将单个文件添加到暂存区
	HUANGQIQIN@HQQ-DELL MINGW64 /g/Java-VIP课程练习/01-GIT/hqq (master)
	$ git add README.md

	# 将所有新增的文件添加到暂存区
	HUANGQIQIN@HQQ-DELL MINGW64 /g/Java-VIP课程练习/01-GIT/hqq (master)
	$ git add -A

**将文件从暂存区中移除（本地文件不会被删除）：git rm --cached <文件名>**

	HUANGQIQIN@HQQ-DELL MINGW64 /g/Java-VIP课程练习/01-GIT/hqq (master)
	$ git rm --cached README.md

**将已经添加到暂存区中的文件提交到本地仓库： git commit <文件名> -m <消息>**
	
	# 命令：$ git commit <文件名> -m <消息>
	HUANGQIQIN@HQQ-DELL MINGW64 /g/Java-VIP课程练习/01-GIT/hqq (master)
	$ git commit README.md -m '首次提交'
	[master (root-commit) 9b2f60b] 首次提交
	 1 file changed, 1 insertion(+)
	 create mode 100644 README.md

**将本地仓库关联到远程仓库：git remote add <远程仓库名> <远程仓库地址>**

	# 添加远程创建关联到本地仓库： 
	HUANGQIQIN@HQQ-DELL MINGW64 /g/Java-VIP课程练习/01-GIT/hqq (master)
	$ git remote add origin https://github.com/hqq518/voice_tally.git

**将本地仓库提交到远程仓库中： git push <仓库名称>**

	# 首次push到远程仓库是会提示需要将本地仓库的master分支设置到远程仓库并在远程仓库中创建一个新的master分支
	HUANGQIQIN@HQQ-DELL MINGW64 /g/Java-VIP课程练习/01-GIT/hqq (master)
	$ git push origin	
	fatal: The current branch master has no upstream branch.
	To push the current branch and set the remote as upstream, use
	
	    git push --set-upstream origin master

	
**将远程仓库克隆到本地： git clone <仓库url>**

	# 首先需要在本地先初始化init，才能使用clone
	HUANGQIQIN@HQQ-DELL MINGW64 /g/Java-VIP课程练习/01-GIT
	$ git clone https://github.com/hqq518/voice_tally.git voice_tally

	# 将本地仓库中所有文件提交到远程仓库
	HUANGQIQIN@HQQ-DELL MINGW64 /g/Java-VIP课程练习/01-GIT/tally/voice_tally (master)
	$ git push --all

**查看当前分支：git branch [-avv]**
	
	HUANGQIQIN@HQQ-DELL MINGW64 /h/学习资料/图灵学习-VIP课程-第二期/Java-VIP课程练习/01-GIT/hqq (master)
	$ git branch
	* master		
	HUANGQIQIN@HQQ-DELL MINGW64 /h/学习资料/图灵学习-VIP课程-第二期/Java-VIP课程练习/01-GIT/hqq (master)
	$ git branch -avv
	* master                9b2f60b 首次提交				<=== 当前本地分支名称
	  remotes/origin/master b6dd61b Initial commit		<=== 关联远程仓库分支名称

	
**基于当前分支新建分支**

	git branch <branch name>

**基于提交新建分支**

	git branch <branch name> <commit id>

**删除分支**

	git branch -d {dev}

**切换分支**

	git checkout <branch name>

**合并分支**

	git merge <merge target>

	#解决冲突，如果因冲突导致自动合并失败，此时 status 为mergeing 状态.
	#需要手动修改后重新提交（commit） 

## 远程仓库管理

**查看远程配置** 

	git remote [-v]

**添加远程地址**

	git remote add origin http:xxx.xxx

**删除远程地址**

	git remote remove origin 

**上传新分支至远程**

	git push --set-upstream origin master 

**将本地分支与远程建立关联**

	git branch --track --set-upstream-to=origin/test test

## tag 管理

**查看当前**

	git tag

**创建分支**

	git tag <tag name> <branch name>

**删除分支**

	git tag -d <tag name>

## 日志管理

**查看当前分支下所有提交日志**

	git log

**查看当前分支下所有提交日志**

	git log {branch}

**单行显示日志**

	git log --oneline

**比较两个版本的区别**

	git log master..experiment

**以图表的方式显示提交合并网络**

	git log --pretty=format:'%h %s' --graph

### git与SVN对比示意图
![示意图](http://git.jiagouedu.com/java-vip/Document/raw/master/%e4%ba%92%e8%81%94%e7%bd%91%e5%b7%a5%e7%a8%8b%e4%b8%93%e9%a2%98/01-Git%e5%9f%ba%e6%9c%ac%e6%a6%82%e5%bf%b5%e4%b8%8e%e6%a0%b8%e5%bf%83%e5%91%bd%e4%bb%a4%e6%8e%8c%e6%8f%a1-%e9%b2%81%e7%8f%ad/git%20push%e6%b5%81%e7%a8%8b.png)