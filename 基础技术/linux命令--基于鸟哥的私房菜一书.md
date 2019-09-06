##第二章： 主机规划与磁盘分区
在Linux系统中，每个设备都被当成一个文件来对待，位于/dev/文件夹下，有自己的命名方式。

##### 关于文件系统的具体结构，以及作用，参看之前的笔记。

##第四章：线上求助
##### 命令格式：

	command [-options] parameter1 parameter2 ...

##### 重要的快捷键
	[Tab]				“命令补全”与“文件补齐”：连按两下，显示所有命令。
	[ctrl]-c				指令中断
	[Ctrl]-d				直接离开了，相当于输入exit。
	[Shift]+[Page Up] 	来往前翻页，也能够使用 
	[Shift]+[Page Down] 	来往后翻页！


#####帮助指令
	//指令使用帮助
	--help
	man command
	info command

	/string 	向“下”搜寻 string 这个字串
	?string 	向“上”搜寻 string 这个字串
	q 		结束这次的 man page
	// 利用 / 或 ? 来搜寻字串时，可以用 n 来继续下一个搜寻，N 来进行“反向”搜寻

	// 关机常用指令
	sync 					将数据同步写入硬盘中的指令
	shutdown				惯用的关机指令
	reboot, halt, poweroff	重新开机，关机：

##第五章：文件权限与目录配置
#####拥有者、群组、其他人：u,g,o
![](https://upload-images.jianshu.io/upload_images/13390267-27e3d1cf72e6d7fd.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

	chgrp 		改变文件所属群组
	chown 		改变文件拥有者
	chmod 		改变文件的权限
	
	// 权限与数字之间的对应关系
	r:4 w:2 x:1
	
	// 示例
	chmod 777 file
	chmod u=rwx,go=rx file
	// 所有人都加上相应指令
	chmod a+w file
	chmod a-x  file

	./ 		代表“本目录”的意思

	uname -r    查看核心版本

##第六章：文件与目录管理
	. 代表此层目录
	.. 代表上一层目录
	- 代表前一个工作目录
	~ 代表“目前使用者身份”所在的主文件夹
	~account 代表 account 这个使用者的主文件夹（account是个帐号名称）

	// 目录相关
	cd：变换目录
	pwd：显示目前的目录
	mkdir：创建一个新的目录
	rmdir：删除一个空的目录

	// 系统路径变量：
	$PATH
	PATH="${PATH}:/root"

	// 文件与目录的检视： ls
	ls -ls
	ls -ah

	// 复制、删除与移动： 
	cp source target
	rm source target
	mv source target

	// 望名知意
	basename /etc/sysconfig/network
	dirname /etc/sysconfig/network

	// 浏览文本内容相关
	cat 		由第一行开始显示文件内容
	head 		只看头几行
	tail 			只看尾巴几行
		

	// 以下不常用
	tac 		从最后一行开始显示，可以看出 tac 是 cat 的倒着写！
	nl		 显示的时候，顺道输出行号！
	more 		一页一页的显示文件内容
	less 		与 more 类似，他可以往前翻页！
	od 			以二进制的方式读取文件内容！

	// 查看日志必学:
	tail -f /var/log/messages


	head -n 20 /etc/man_db.conf | tail -n 10 
	// 这样就可以得到第 11 到第 20 行之间的内容了

	touch  		修改文件时间或创建新文件


	umask	文件默认权限
	file		观察文件类型


##### 目前先知道有这么两个命令就好
	chattr （设置文件隐藏属性）
	lsattr （显示文件隐藏属性）
	SUID ， SGID， SBIT 




#####指令与文件的搜寻
	which 		寻找“可执行文件”
	whereis 		文件或目录名
	find

##第八章：打包与备份

	*.Z compress 程序压缩的文件；
	*.zip zip 程序压缩的文件；
	*.gz gzip 程序压缩的文件；
	*.bz2 bzip2 程序压缩的文件；
	*.xz xz 程序压缩的文件；
	*.tar tar 程序打包的数据，并没有压缩过；
	*.tar.gz tar 程序打包的文件，其中并且经过 gzip 的压缩
	*.tar.bz2 tar 程序打包的文件，其中并且经过 bzip2 的压缩
	*.tar.xz tar 程序打包的文件，其中并且经过 xz 的压缩


	tar 		可以将很多文件“打包”成为一个文件
	gzip [-cdtv#] 文件名
	bzip2 [-cdkzv#] 文件名

	压　缩：tar -j<u>c</u>v -f filename.tar.bz2 要被压缩的文件或目录名称
	查　询：tar -j<u>t</u>v -f filename.tar.bz2
	解压缩：tar -j<u>x</u>v -f filename.tar.bz2 -C 欲解压缩的目录


##第十章、认识与学习BASH

	//命令别名设置功能：
	alias lm='ls -al'

	//查询指令是否为 Bash shell 的内置命令： 
	type	command

	env 		//观察环境变量与常见环境变量说明

	$			//关于本 shell 的 PID
	echo $$ 		//打印当前用户的你的 PID 号码。

	?：		//关于上个执行指令的回传值
	// 如果成功的执行该指令， 则会回传一个 0 值，错误返回非0值

	export： 自订变量转成环境变量

![](https://upload-images.jianshu.io/upload_images/13390267-3e93a643f92830cc.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

		export 变量名称	// 导出对应变量
		export 			// 导出所有变量

##### 影响显示结果的语系变量 （locale）
 	read	读取来自键盘输入

	declare [-aixr] variable
	选项与参数：
	-a ：将后面名为 variable 的变量定义成为 array类型
	-i ：将后面名为 variable 的变量定义成为integer 类型
	-x ：用法与 export 一样，就是将后面的 variable 变成环境变量；
	-r ：将变量设置成为 readonly 类型，该变量不可被更改内容，也不能 unset


	array 	数组变量类型


##### 与文件系统及程序的限制关系： ulimit
可以考虑用 ulimit 来限制使用者可以创建的文件大小。

##### 变量相关的一些处理符号
![](https://upload-images.jianshu.io/upload_images/13390267-7c852aef6e258160.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![](https://upload-images.jianshu.io/upload_images/13390267-f63285b41f73d7aa.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

	alias, unalias 	命令别名设置
	history			历史命令

	~/.bash_history 	//记录历史命令，所以要小心你在命令中输入的密码被盗取哦。
#####路径与指令搜寻顺序

1. 以相对/绝对路径执行指令，例如“ /bin/ls ”或“ ./ls ”；
2. 由 alias 找到该指令来执行；
3. 由 bash 内置的 （builtin） 指令来执行；
4. 通过 $PATH 这个变量的顺序搜寻到的第一个指令来执行。

##### bash 的进站与欢迎讯息： /etc/issue, /etc/motd
比如阿里云服务器的进站欢迎信息。

##### 包含PATH的系统配置信息：
1. /etc/profile：这是系统整体的设置，你最好不要修改这个文件；
2. ~/.bash_profile 或 ~/.bash_login 或 ~/.profile：属于使用者个人设置，
	
		// 一般只有以下一个
		1. ~/.bash_profile
		2. ~/.bash_login
		3. ~/.profile
![](https://upload-images.jianshu.io/upload_images/13390267-a63d7517b53fcb98.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

##### source 或者 .  :配置的环境变量马上生效。

##### 万用字符与特殊符号
![](https://upload-images.jianshu.io/upload_images/13390267-7c612692a9d2a519.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

	// 示例
	ll -d /etc/*[0-9]* &lt;==记得中括号左右两边均需 *
	ll -d /etc/????? &lt;==由于 ? 一定有一个，所以五个 ? 就对了
	ll -d /etc/cron* &lt;==加上 -d 是为了仅显示目录而已

![](https://upload-images.jianshu.io/upload_images/13390267-aa5ecc615b3d5a6e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

##### 输入与输出
1. 标准输入　　（stdin） ：代码为 0 ，使用 < 或 << ；
2. 标准输出　　（stdout）：代码为 1 ，使用 > 或 >> ；
3. 标准错误输出（stderr）：代码为 2 ，使用 2> 或 2>> ；

##### 几个很常用的命令
	cmd ; cmd 	// ; 连接多个命令
	$? 		（指令回传值） 与 && 或 ||
	 |		管线命令 （pipe）

![](https://upload-images.jianshu.io/upload_images/13390267-d7f6ce389484a83b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



##### 撷取命令： cut, grep
cut 是将一行讯息当中，取出某部分我们想要的

grep 则是分析一行讯息， 若当中有我们所需要的信息，就将该行拿出来。

##### 后处理命令
	sort 		排序
	uniq		去重
	wc 			统计行，字，字符

##### 双向重导向： tee
![](https://upload-images.jianshu.io/upload_images/13390267-c4954dbbc4a47996.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

##### split将大文件分区成为小文件了
	//范例一：我的 /etc/services 有六百多K，若想要分成 300K 一个文件时？
	$ cd /tmp; split -b 300k /etc/services services


##第十一章：正则表达式
![](https://upload-images.jianshu.io/upload_images/13390267-707571ac87086a02.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![](https://upload-images.jianshu.io/upload_images/13390267-650829bdc1aa9526.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#####比較文件差異。
	diff [-bBi] from-file to-file
	cmp passwd.old passwd.new

diff主要是以“行”为单位比对， cmp 则是以“字节”为单位去比对，这并不相同！）

##第十二章：Shell Scripts
本章的學習可以參看之前的shell筆記，并且理解shell只是命令行的封装。最好的学习方式应该是知道基础后参考别人的程序写出自己想要的程序。

##第十三章：Linux 帐号管理与 ACL 权限设置
	/etc/passwd		// 存放用户
	/etc/group		// 存放组
	/etc/shadow		// 存放用户密码
	/etc/gshadow	// 存放组密码

root 密码忘记了：重新开机进入单人维护模式，此时再以 passwd 修改密码即可。或以 Live CD 开机后挂载根目录去修改/etc/shadow，将里面的 root 的密码字段清空， 再重新开机后 root 将不用密码即可登陆。然后修改密码。

##### 用户组相关的命令
	groups		查看当前用户所属组，可以有多个。
	newgrp:		有效群组的切换

	// 用户相关
	useradd
	passwd
	usermod：修改用户属性，修改组就靠你了。
	userdel

	// 以下命令参考user类似
	groupadd
	groupmod
	groupdel

	id 	这个指令则可以查询某人或自己的相关 UID/GID 等等的信息

chage提供比passwd -S 更详细的密码参数显示功能。

##### su 切换用户
##### sudo 获取root权限，sudo 的执行则仅需要自己的密码即可！
需要在/etc/sudoers中配置，或者直接使用命令visudo。

	使用者帐号 登陆者的来源主机名称=（可切换的身份） 可下达的指令
	root ALL=（ALL） ALL 	//这是默认值
	vbird1 ALL=（ALL） ALL 	//这一行是你要新增的！

但是一般不建议这么做，除非你需要很细粒度的控制。可采用加入组的方式

#####在最左边加上 % ，代表“群组”
	%wheel ALL=（ALL） ALL &lt;==大约在 106 行左右，请将这行的 # 拿掉！
	%wheel ALL=（ALL） NOPASSWD: ALL 
	NOPASSWD：sudo时候免除输入密码

这样在该群组中的用户就可以获取root权限了。

##### 主机上的使用者讯息传递
	w
	who
	last
	lastlog

如果您想要知道每个帐号的最近登陆的时间，则可以使用 lastlog

## 第十五章：例行性工作调度（crontab）
本章的内容与shell一样，参看之前的笔记，同时，对于其他几个工作调度功能，等实际用到的时候再学习。

## 第十六章、程序管理
	cp file1 file2 & 
	//这个 & 代表：“将工作丢到背景中去执行”

	[ctrl]-z 	丢到背景中，且暂停

	jobs			观察目前的背景工作状态：
	fg：fg %jobnumber		将背景工作拿到前景来处理：
	bg：bg %jobnumber		让工作在背景下的状态变成运行中 
	kill			管理背景当中的工作： 

	// 示例
	kill -9 %2 强制关闭
	kill -15 %2 优雅关闭

kill 后面接的数字默认会是 PID ，如果想要管理 bash 的工作控制，就得要加上 %数字


##### 程序的观察---静态的 ps 、动态的 top
	ps ：将某个时间点的程序运行情况撷取下来	
	top 则可以持续侦测程序运行的状

	top: top -d 2	每两秒钟更新一次

top 默认使用 CPU 使用率 （%CPU） 作为排序的重点，如果你想要使用内存使用率排序，则可以按下“M”， 若要回复则按下“P”即可。如果想要离开 top 则按下“ q ”吧！

	pstree: 要找程序之间的相关性

	killall [-iIe] [command name]

最简单的方法就是利用 killall ， 因为他可以将系统当中所有以某个指令名
称启动的程序全部删除。


##### 关于程序的执行顺序（优先级）

	PRI（new） = PRI（old） + nice

我们只能改变nice值：

	nice [-n 数字] command

-  nice 值可调整的范围为 -20 ~ 19 ；
-  root 可随意调整自己或他人程序的 Nice 值，且范围为 -20 ~ 19 ；
-  一般使用者仅可调整自己程序的 Nice 值，且范围仅为 0 ~ 19 （避免一般用户抢占系统资源）；	
- 一般使用者仅可将 nice 值越调越高，例如本来 nice 为 5 ，则未来仅能调整到大于 5；

	// 已存在程序的 nice 重新调整
	renice [number] PID

	free 		观察内存使用情况
	uname		查阅系统与核心相关信息	
	netstat 		追踪网络或插槽档
	uptime		观察系统启动时间与工作负载
	vmstat 		侦测系统资源变化

## 认识系统服务 （daemons）
	service: http
	serviced:  httpd
	daemons


	systemctl [command] [unit]
	
	command 主要有：
	start ：立刻启动后面接的 unit
	stop ：立刻关闭后面接的 unit
	restart ：立刻关闭后启动后面接的 unit，亦即执行 stop 再 start 的意思
	reload ：不关闭后面接的 unit 的情况下，重新载入配置文件，让设置生效
	enable ：设置下次开机时，后面接的 unit 会被启动
	disable ：设置下次开机时，后面接的 unit 不会被启动
	status ：目前后面接的这个 unit 的状态，会列出有没有正在执行、开机默认执行否、登录等信息等！
	is-active ：目前有没有正在运行中
	is-enable ：开机时有没有默认要启用这个 unit



