# Linux学习笔记
2/8/2018 10:03:13 PM 

------------------------------
查看当前登录系统支持的语系

	[hqq@study 桌面]$ locale
	LANG=zh_CN.UTF-8
	LC_CTYPE="zh_CN.UTF-8"
	LC_NUMERIC="zh_CN.UTF-8"
	LC_TIME="zh_CN.UTF-8"
	LC_COLLATE="zh_CN.UTF-8"
	LC_MONETARY="zh_CN.UTF-8"
	LC_MESSAGES="zh_CN.UTF-8"
	LC_PAPER="zh_CN.UTF-8"
	LC_NAME="zh_CN.UTF-8"
	LC_ADDRESS="zh_CN.UTF-8"
	LC_TELEPHONE="zh_CN.UTF-8"
	LC_MEASUREMENT="zh_CN.UTF-8"
	LC_IDENTIFICATION="zh_CN.UTF-8"
	LC_ALL=

查看当前时间
	
	[hqq@study 桌面]$ date
	2018年 02月 08日 星期四 21:58:26 CST

修改当前语系为英文语系

	[hqq@study 桌面]$ LANG=en_US.utf-8
	[hqq@study 桌面]$ export LC_ALL=en_US.utf-8

再次查看当前时间

	[hqq@study 桌面]$ date
	Thu Feb  8 21:59:51 CST 2018

日期格式化成yyyy-mm-dd输出
	
	[hqq@study 桌面]$ date +%Y-%m-%d
	2018-02-08

显示指定年份的所有日历

	[hqq@study 桌面]$ cal 2018
	                               2018                               
	
	       January               February                 March       
	Su Mo Tu We Th Fr Sa   Su Mo Tu We Th Fr Sa   Su Mo Tu We Th Fr Sa
	    1  2  3  4  5  6                1  2  3                1  2  3
	 7  8  9 10 11 12 13    4  5  6  7  8  9 10    4  5  6  7  8  9 10
	14 15 16 17 18 19 20   11 12 13 14 15 16 17   11 12 13 14 15 16 17
	21 22 23 24 25 26 27   18 19 20 21 22 23 24   18 19 20 21 22 23 24
	28 29 30 31            25 26 27 28            25 26 27 28 29 30 31
	
	        April                   May                   June        
	Su Mo Tu We Th Fr Sa   Su Mo Tu We Th Fr Sa   Su Mo Tu We Th Fr Sa
	 1  2  3  4  5  6  7          1  2  3  4  5                   1  2
	 8  9 10 11 12 13 14    6  7  8  9 10 11 12    3  4  5  6  7  8  9
	15 16 17 18 19 20 21   13 14 15 16 17 18 19   10 11 12 13 14 15 16
	22 23 24 25 26 27 28   20 21 22 23 24 25 26   17 18 19 20 21 22 23
	29 30                  27 28 29 30 31         24 25 26 27 28 29 30
	
	        July                  August                September     
	Su Mo Tu We Th Fr Sa   Su Mo Tu We Th Fr Sa   Su Mo Tu We Th Fr Sa
	 1  2  3  4  5  6  7             1  2  3  4                      1
	 8  9 10 11 12 13 14    5  6  7  8  9 10 11    2  3  4  5  6  7  8
	15 16 17 18 19 20 21   12 13 14 15 16 17 18    9 10 11 12 13 14 15
	22 23 24 25 26 27 28   19 20 21 22 23 24 25   16 17 18 19 20 21 22
	29 30 31               26 27 28 29 30 31      23 24 25 26 27 28 29
	                                              30
	       October               November               December      
	Su Mo Tu We Th Fr Sa   Su Mo Tu We Th Fr Sa   Su Mo Tu We Th Fr Sa
	    1  2  3  4  5  6                1  2  3                      1
	 7  8  9 10 11 12 13    4  5  6  7  8  9 10    2  3  4  5  6  7  8
	14 15 16 17 18 19 20   11 12 13 14 15 16 17    9 10 11 12 13 14 15
	21 22 23 24 25 26 27   18 19 20 21 22 23 24   16 17 18 19 20 21 22
	28 29 30 31            25 26 27 28 29 30      23 24 25 26 27 28 29
	                                              30 31
	
显示指定月份和年份的日历

	[hqq@study 桌面]$ cal 2 2018
	    February 2018   
	Su Mo Tu We Th Fr Sa
	             1  2  3
	 4  5  6  7  8  9 10
	11 12 13 14 15 16 17
	18 19 20 21 22 23 24
	25 26 27 28

进入计算器

	[hqq@study 桌面]$ bc
	bc 1.06.95
	Copyright 1991-1994, 1997, 1998, 2000, 2004, 2006 Free Software Foundation, Inc.
	This is free software with ABSOLUTELY NO WARRANTY.
	For details type `warranty'. 
	100+12+13
	125

退出计算器

	quit

计算器默认计算结果是整数，若需要计算小数，则设置scale=保存小数个数

	[hqq@study 桌面]$ bc
	bc 1.06.95
	Copyright 1991-1994, 1997, 1998, 2000, 2004, 2006 Free Software Foundation, Inc.
	This is free software with ABSOLUTELY NO WARRANTY.
	For details type `warranty'. 
	scale=3			//就是这里设置保存3位小数
	1/3
	.333




