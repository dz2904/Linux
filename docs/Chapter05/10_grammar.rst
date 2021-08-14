Shell 编程语法
####################################

先来看个例子吧：

::

    #!/bin/bash
    cd ~
    mkdir shell_tut
    cd shell_tut
        
    for ((i=0; i<10; i++)); do
        touch test_$i.txt
    done

- 第1行：指定脚本解释器，这里是用 Bash 做解释器的
- 第2行：切换到当前用户的 home 目录
- 第3行：创建一个目录 shell_tut
- 第4行：切换到 shell_tut 目录
- 第5行：循环条件，一共循环 10 次
- 第6行：创建 test_0…9.txt 文件
- 第7行：循环体结束

mkdir, touch 都是系统自带的程序，一般在 /bin 或者 /usr/bin 目录下。for, do, done 是 Shell 脚本语言的关键字。


运行 Shell 脚本有两种方法：

**作为可执行程序**

::

	chmod +x test.sh
	./test.sh

注意，一定要写成 ./test.sh，而不是 test.sh，运行其它二进制的程序也一样，直接写 test.sh，linux 系统会去 PATH 里寻找有没有叫 test.sh 的，而只有 /bin, /sbin, /usr/bin，/usr/sbin 等在 PATH 里，你的当前目录通常不在 PATH 里，所以写成 test.sh 是会找不到命令的，要用 ./test.sh 告诉系统说，就在当前目录找。

通过这种方式运行 bash 脚本，第一行一定要写对，好让系统查找到正确的解释器。

这里的"系统"，其实就是 shell 这个应用程序（想象一下 Windows Explorer），但我故意写成系统，是方便理解，既然这个系统就是指 shell，那么一个使用 /bin/sh 作为解释器的脚本是不是可以省去第一行呢？是的。

**作为解释器参数**

这种运行方式是，直接运行解释器，其参数就是 shell 脚本的文件名，如：

::

	/bin/sh test.sh
	/bin/php test.php

这种方式运行的脚本，不需要在第一行指定解释器信息，写了也没用。


变量
************************************

定义变量
====================================

定义变量时，变量名不加美元符号（$），如：

::

	your_name="qinjx"

注意，变量名和等号之间不能有空格，这可能和你熟悉的所有编程语言都不一样。

除了显式地直接赋值，还可以用语句给变量赋值，如：

::

	for file in `ls /etc`

使用变量
====================================

使用一个定义过的变量，只要在变量名前面加美元符号即可，如：

::

	your_name="qinjx"
	echo $your_name
	echo ${your_name}

变量名外面的花括号是可选的，加不加都行，加花括号是为了帮助解释器识别变量的边界，比如下面这种情况：

::

	for skill in Ada Coffe Action Java; do
		echo "I am good at ${skill}Script"
	done

如果不给 skill 变量加花括号，写成 echo "I am good at $skillScript"，解释器就会把 $skillScript 当成一个变量（其值为空），代码执行结果就不是我们期望的样子了。

推荐给所有变量加上花括号，这是个好的编程习惯。IntelliJ IDEA 编写 shell script 时，IDE 就会提示加花括号。

重定义变量
====================================

已定义的变量，可以被重新定义，如：

::

	your_name="qinjx"
	echo $your_name
	
	your_name="alibaba"
	echo $your_name
	
这样写是合法的，但注意，第二次赋值的时候不能写 $your_name="alibaba"，使用变量的时候才加美元符。

注释
************************************

以“#”开头的行就是注释，会被解释器忽略。

多行注释
====================================

sh 里没有多行注释，只能每一行加一个 # 号。就像这样：

::

	#--------------------------------------------
	# 这是一个自动打ipa的脚本，基于webfrogs的ipa-build书写
	
	# 功能：自动为etao ios app打包，产出物为14个渠道的ipa包
	# 特色：全自动打包，不需要输入任何参数
	#--------------------------------------------
	
	##### 用户配置区 开始 #####
	#
	#
	# 项目根目录，推荐将此脚本放在项目的根目录，这里就不用改了
	# 应用名，确保和Xcode里Product下的target_name.app名字一致
	#
	##### 用户配置区 结束  #####

如果在开发过程中，遇到大段的代码需要临时注释起来，过一会儿又取消注释，怎么办呢？每一行加个#符号太费力了，可以把这一段要注释的代码用一对花括号括起来，定义成一个函数，没有地方调用这个函数，这块代码就不会执行，达到了和注释一样的效果。

字符串
************************************

字符串是 shell 编程中最常用最有用的数据类型（除了数字和字符串，也没啥其它类型好用了，哈哈），字符串可以用单引号，也可以用双引号，也可以不用引号。单双引号的区别跟 PHP 类似。

**单引号**

::

	str='this is a string'

单引号字符串的限制：

- 单引号里的任何字符都会原样输出，单引号字符串中的变量是无效的
- 单引号字串中不能出现单引号（对单引号使用转义符后也不行）
 
**双引号**

::

	your_name='qinjx'
	str="Hello, I know your are \"$your_name\"! \n"

- 双引号里可以有变量
- 双引号里可以出现转义字符

字符串操作
====================================

**拼接字符串**

::
	
	your_name="qinjx"
	greeting="hello, "$your_name" !"
	greeting_1="hello, ${your_name} !"
	
	echo $greeting $greeting_1

**获取字符串长度**

::

	string="abcd"
	echo ${#string} #输出：4

**提取子字符串**

::

	string="alibaba is a great company"
	echo ${string:1:4} #输出：liba

**查找子字符串**

::

	string="alibaba is a great company"
	# 找出字母 i 的位置
	echo `expr index "$string" is`
	3

**更多** 参见本文档末尾的参考资料中[Advanced Bash-Scripting Guid Chapter 10.1](http://tldp.org/LDP/abs/html/string-manipulation.html)

## 数组

## 管道

## 条件判断

流程控制
************************************

和 Java、PHP 等语言不一样，sh 的流程控制不可为空，如：

::

	<?php
	if (isset($_GET["q"])) {
		search(q);
	}
	else {
		//do nothing
	}

在 sh/bash 里可不能这么写，如果 else 分支没有语句执行，就不要写这个 else。

还要注意，sh 里的 if [ $foo -eq 0 ]，这个方括号跟 Java/PHP 里 if 后面的圆括号大不相同，它是一个可执行程序（和 ls, grep 一样），想不到吧？在 CentOS 上，它在 /usr/bin 目录下：

::

	ll /usr/bin/[
	-rwxr-xr-x. 1 root root 33408 6月  22 2012 /usr/bin/[

正因为方括号在这里是一个可执行程序，方括号后面必须加空格，不能写成if [$foo -eq 0]

if else
====================================

**if**

::

	if condition
	then
		command1 
		command2
		...
		commandN 
	fi

写成一行（适用于终端命令提示符）：

::

	if `ps -ef | grep ssh`;  then echo hello; fi
	
末尾的 fi 就是 if 倒过来拼写，后面还会遇到类似的

**if else**

::

	if condition
	then
		command1 
		command2
		...
		commandN
	else
		command
	fi

**if else-if else**

::

	if condition1
	then
		command1
	elif condition2
		command2
	else
		commandN
	fi

for while
====================================

**for**

在开篇的示例里演示过了：

::

	for var in item1 item2 ... itemN
	do
		command1
		command2
		...
		commandN
	done

写成一行：

::

	for var in item1 item2 ... itemN; do command1; command2… done;

**C 风格的 for**

::

	for (( EXP1; EXP2; EXP3 ))
	do
		command1
		command2
		command3
	done

**while**

::

	while condition
	do
		command
	done
	
**无限循环**

::

	while :
	do
		command
	done

或者

::

	while true
	do
		command
	done

或者

::

	for (( ; ; ))

**until**

::

	until condition
	do
		command
	done

**case**

::

	case "${opt}" in
		"Install-Puppet-Server" )
			install_master $1
			exit
		;;

		"Install-Puppet-Client" )
			install_client $1
			exit
		;;

		"Config-Puppet-Server" )
			config_puppet_master
			exit
		;;

		"Config-Puppet-Client" )
			config_puppet_client
			exit
		;;

		"Exit" )
			exit
		;;

		* ) echo "Bad option, please choose again"
	esac

case 的语法和 C family 语言差别很大，它需要一个 esac（就是 case 反过来）作为结束标记，每个 case 分支用右圆括号，用两个分号表示 break


## 函数
### 定义
### 调用

文件包含
************************************

可以使用 source 和 . 关键字，如：

::

	source ./function.sh
	. ./function.sh

在 bash 里，source 和 . 是等效的，他们都是读入 function.sh 的内容并执行其内容（类似 PHP 里的 include），为了更好的可移植性，推荐使用第二种写法。

包含一个文件和执行一个文件一样，也要写这个文件的路径，不能光写文件名，比如上述例子中:

::

	. ./function.sh

不可以写作：

::

	. function.sh

如果 function.sh 是用户传入的参数，如何获得它的绝对路径呢？方法是：

::

	real_path=`readlink -f $1`#$1是用户输入的参数，如function.sh
	. $real_path


## 用户输入
### 执行脚本时传入
### 脚本运行中输入
### select菜单

## stdin和stdout


参考资料
************************************

- `Advanced Bash-Scripting Guide <http://tldp.org/LDP/abs/html/>`_ 非常详细，非常易读，大量 example，既可以当入门教材，也可以当做工具书查阅
- `Unix Shell Programming <http://www.tutorialspoint.com/unix/unix-shell.htm`_
- `Linux Shell Scripting Tutorial - A Beginner's handbook <http://bash.cyberciti.biz/guide/Main_Page>`_
