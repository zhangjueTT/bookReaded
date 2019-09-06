## 基本使用
#####新建脚本
文件以 xxx.sh 命名，结尾为 .sh
<br>sh里没有多行注释，只能每一行加一个#号。
<br>开发过程中，遇到大段的代码需要临时注释起来，可以把这一段要注释的代码用一对花括号括起来，定义成一个函数，没有地方调用这个函数，达到了和注释一样的效果。

示例：
		
	#!/bin/bash
	echo "Hello World !"

##### 脚本的执行

	# 直接执行
	chmod +x ./test.sh  #使脚本具有执行权限
	./test.sh  			#执行脚本
	# sh执行
	/bin/sh test.sh

##### 变量

	# 定义变量时不要加空格
	your_name="runoob.com"
	# 使用变量时需要$符号，{}可加可不加
	echo $your_name
	echo ${your_name}
	# 删除变量
	unset your_name

	# 变量只读，不能被改变
	readonly your_name
	
##### 字符串
字符串可以用单引号，双引号，也可以不用引号。**单引号，不能连接变量，不能转义，双引号可以。**

	your_name="zhangjue"
	
	# 使用双引号拼接
	greeting="hello, "$your_name" !"
	greeting_1="hello, ${your_name} !"
	echo $greeting  $greeting_1
	
	# 使用单引号拼接
	greeting_2='hello, '$your_name' !'
	greeting_3='hello, ${your_name} !'
	echo $greeting_2  $greeting_3

输出为：
	
	hello, zhangjue ! hello, zhangjue !
	hello, zhangjue ! hello, ${your_name} !

提取字符串1-4位置（从0开始）

	${string:1:4}
	
#####数组：
	
	# 仅支持1维数组
	# 数组的初始化
	array_name=(value0 value1 value2 value3)
	array_name[0]=value0
	array_name[1]=value1
	array_name[2]=value2

	# 获取元素	
	valuen=${array_name[n]}
	# 获取数组中所有元素
	echo ${array_name[@]}
	# 所有元素长度，单元素长度
	length=${#array_name[*]}
	lengthn=${#array_name[n]}
	
	

#####获取外部传递过来的参数：
	
	echo "执行的文件名：$0";
	echo "第一个参数为：$1";
	echo "第二个参数为：$2";
	echo "第三个参数为：$3";
	
- $#	传递到脚本的参数个数
- $$	脚本运行的当前进程ID号
- $!	后台运行的最后一个进程的ID号
- $*	以一个单字符串显示所有向脚本传递的参数。传递一个参数
	<br>如"$*"用「"」括起来的情况、以"$1 $2 … $n"的形式输出所有参数。
- $@	与$*相同，但是使用时加引号，并在引号中返回每个参数。传递n个参数
	<br>如"$@"用「"」括起来的情况、以"$1" "$2" … "$n" 的形式输出所有参数。
- $-	显示Shell使用的当前选项，与set命令功能相同。
- ##### $?	显示最后命令的退出状态。0表示没有错误，其他任何值表明有错误。 

## echo相关指令

	# 从外界读取一个变量，存放到name中
	read name 
	echo "$name It is a test"
	echo -e "OK! \n" 			# -e 开启转义 \n 换行
	echo -e "OK! \c" 			# -e 开启转义 \c 不换行
	echo "It is a test" > myfile	# 显示的结果重定向到myfile文件中
	echo `date`					# 显示命令执行结果


## printf命令

默认 printf 不会像 echo 自动添加换行符，我们可以手动添加 \n。

#####语法：
printf  format-string  [arguments...]


%s %c %d %f都是格式替代符，-表示左对齐，没有则表示右对齐

- d: Decimal 十进制整数 -- 对应位置参数必须是十进制整数，否则报错！
- s: String 字符串 -- 对应位置参数必须是字符串或者字符型，否则报错！
- c: Char 字符 -- 对应位置参数必须是字符串或者字符型，否则报错！
- f: Float 浮点 -- 对应位置参数必须是数字型，否则报错！

示例：
	
	printf "%-10s %-8s %-4s\n" 姓名 性别 体重kg  



## test 命令
Shell中的 test 命令用于检查某个条件是否成立，它可以进行数值、字符和文件三个方面的测试。

	if test $[num1] -eq $[num2]

和括号一个作用？，目前感觉该方法比较鸡肋。


## Shell 基本运算符
##### 算数运算符

原生bash不支持简单的数学运算，但是可以通过其他命令来实现，例如 awk 和 expr，expr 最常用。expr 是一款表达式计算工具，使用它能完成表达式的求值操作。

	val=`expr $a + $b`
	echo "a + b : $val"
	
	val=`expr $a - $b`
	echo "a - b : $val"
	
	# *号前需要加\ 转义
	val=`expr $a \* $b`
	echo "a * b : $val"
	
	val=`expr $b / $a`
	echo "b / a : $val"
	
	val=`expr $b % $a`
	echo "b % a : $val"
	
##### 关系运算符
1. -eq	检测两个数是否相等，相等返回 true。	
	[ $a -eq $b ] 返回 false。
2. -ne	检测两个数是否不相等，不相等返回 true。	[ $a -ne $b ] 返回 true。
3. -gt	检测左边的数是否大于右边的，如果是，则返回 true。	[ $a -gt $b ] 返回 false。
4. -lt	检测左边的数是否小于右边的，如果是，则返回 true。	[ $a -lt $b ] 返回 true。
5. -ge	检测左边的数是否大于等于右边的，如果是，则返回 true。	[ $a -ge $b ] 返回 false。
6. -le	检测左边的数是否小于等于右边的，如果是，则返回 true。	[ $a -le $b ] 返回 true。


##### 布尔运算符
1. ! 非运算，表达式为 true 则返回 false，否则返回 true。	[ ! false ] 返回 true。
2. -o	或运算，有一个表达式为 true 则返回 true。	[ $a -lt 20 -o $b -gt 100 ] 返回 true。
3. -a	与运算，两个表达式都为 true 才返回 true。	[ $a -lt 20 -a $b -gt 100 ] 返回 false。

##### 逻辑运算符
1. &&	逻辑的 AND	
	[[ $a -lt 100 && $b -gt 100 ]] 返回 false
2. ||	逻辑的 OR	
	[[ $a -lt 100 || $b -gt 100 ]] 返回 true


#### 字符串运算符
1. =	检测两个字符串是否相等，相等返回 true。	[ $a = $b ] 返回 false。
2. !=	检测两个字符串是否相等，不相等返回 true。	[ $a != $b ] 返回 true。
3. -z	检测字符串长度是否为0，为0返回 true。	
	[ -z $a ] 返回 false。
4. -n	检测字符串长度是否为0，不为0返回 true。	[ -n "$a" ] 返回 true。
5. str	检测字符串是否为空，不为空返回 true。	
	[ $a ] 返回 true。

##### 文件测试运算符

1. -b file	检测文件是否是块设备文件，如果是，则返回 true。	[ -b $file ] 返回 false。
2. -c file	检测文件是否是字符设备文件，如果是，则返回 true。	[ -c $file ] 返回 false。
3. -d file	检测文件是否是目录，如果是，则返回 true。	[ -d $file ] 返回 false。
4. -f file	检测文件是否是普通文件（既不是目录，也不是设备文件），如果是，则返回 true。	[ -f $file ] 返回 true。
5. -g file	检测文件是否设置了 SGID 位，如果是，则返回 true。	[ -g $file ] 返回 false。
6. -k file	检测文件是否设置了粘着位(Sticky Bit)，如果是，则返回 true。	[ -k $file ] 返回 false。
7. -p file	检测文件是否是有名管道，如果是，则返回 true。	[ -p $file ] 返回 false。
8. -u file	检测文件是否设置了 SUID 位，如果是，则返回 true。	[ -u $file ] 返回 false。
9. -r file	检测文件是否可读，如果是，则返回 true。	[ -r $file ] 返回 true。
10. -w file	检测文件是否可写，如果是，则返回 true。	[ -w $file ] 返回 true。
11. -x file	检测文件是否可执行，如果是，则返回 true。	[ -x $file ] 返回 true。
12. -s file	检测文件是否为空（文件大小是否大于0），不为空返回 true。	[ -s $file ] 返回 true。
13. -e file	检测文件（包括目录）是否存在，如果是，则返回 true。	[ -e $file ] 返回 true。


## Shell 流程控制

#####if 语句

	if [ $(ps -ef | grep -c "ssh") -gt 1 ]; then echo "true"; fi
	
	if condition
	then
	    command1 
	    ...
	    commandN
	else
	    command
	fi

	
	if condition1
	then
	    command1
	elif condition2 
	then 
	    command2
	else
	    commandN
	fi

##### for 循环

	for var in item1 item2 ... itemN; do command1; command2… done;

	for var in item1 item2 ... itemN
	do
	    command1
	    command2
	    ...
	    commandN
	done
	
##### while 语句

	while condition
	do
	    command
	done

##### 示例

	#!/bin/bash
	int=1
	while(( $int<=5 ))
	do
	    echo $int
	    let "int++"   # 执行i++, 不需要加$
	done
	
##### 输入时按 <CTRL-D> 可以退出

	echo '按下 <CTRL-D> 退出'
	echo -n '输入你最喜欢的网站名: '
	while read FILM
	do
	    echo "是的！$FILM 是一个好网站"
	done

##### until 循环： 直到 true 的时候停止

	until condition
	do
	    command
	done

##### case 语句:

	case $aNum in
	    1)  echo '你选择了 1'
	    ;;
	    2)  echo '你选择了 2'
	    ;;
	    3)  echo '你选择了 3'
	    ;;
	    4)  echo '你选择了 4'
	    ;;
	    *)  echo '你没有输入 1 到 4 之间的数字'
	    ;;
	esac

##### break命令，continue命令： 与其他语言一致。

## Shell 函数

	[ function ] funname [()]
	{
	
	    action;
	
	    [return int;]
	
	}

1. 可以带function fun() 定义，也可以直接fun() 定义,不带任何参数。
2. 参数返回，可以显示加：return 返回，如果不加，将以最后一条命令运行结果，作为返回值。
3. 函数返回值在调用该函数后通过 $? 来获得。

示例：

	funWithParam(){
	    echo "第一个参数为 $1 !"
	    echo "第二个参数为 $2 !"
	    # echo "第十个参数为 $10 !"
	    echo "第十个参数为 ${10} !"
	    echo "第十一个参数为 ${11} !"
	    echo "参数总数有 $# 个!"
	    echo "作为一个字符串输出所有参数 $* !"
	}
	funWithParam 1 2 3 4 5 6 7 8 9 34 73
	funWithParam # 没有函数参数的函数调用。

#####注意，$10 不能获取第十个参数，获取第十个参数需要${10}。当n>=10时，需要使用${n}来获取参数。


## Shell 输入/输出重定向

1. command > file	将输出重定向到 file。
	<br> $ who > users
2. command < file	将输入重定向到 file。
	<br> 本来需要从键盘获取输入的命令会转移到文件读取内容。
3. command >> file	将输出以追加的方式重定向到 file。
4. n > file	将文件描述符为 n 的文件重定向到 file。
5. n >> file	将文件描述符为 n 的文件以追加的方式重定向到 file。
6. n >& m	将输出文件 m 和 n 合并。
7. n <& m	将输入文件 m 和 n 合并。
8. << tag	将开始标记 tag 和结束标记 tag 之间的内容作为输入。

#####文件描述符：

- 标准输入文件(stdin)：stdin的文件描述符为0，Unix程序默认从stdin读取数据。
- 标准输出文件(stdout)：stdout 的文件描述符为1，Unix程序默认向stdout输出数据。
- 标准错误文件(stderr)：stderr的文件描述符为2，Unix程序会向stderr流中写入错误信息。


示例：

	$ command 2 > file

	# 同时替换输入和输出，执行command1，从文件infile读取内容，然后将输出写入到outfile中。
	command1 < infile > outfile

	
## Shell 文件包含
	. filename   # 注意点号(.)和文件名中间有一空格
	#或
	source filename