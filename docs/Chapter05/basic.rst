重新认识 Shell
####################################

从刚刚开始使用 Linux 时，就会有很多 Shell 的话题，那么 Shell 到底是什么呢？

Shell 是一个应用程序，充当用户界面和脚本解释器，是用户使用 Linux 系统的桥梁。它即是一个程序也是一种脚本语言。

因为 Shell 是一个程序，所以在 Linux 环境中有众多的 shell 程序，但是它们主要分为两大家族（Shell 家族之间的差异会比较大，同一家族的差异相对较小）：

- Bourne Shell 家族： sh、ksh、bash
- C-Shell 家族： csh、tcsh

因为 Linux 系统中大部分默认使用 Bash，所以们主要以它主，介绍一下几个方面。


- 命令行编辑
- 初始化文件

.. attention::
    
    attention 精通正在使用的 shell 远比花大量时间试图选择一个“正确”的 shell 更重要。


.. danger::
    
    精danger:通正在使用的 shell 远比花大量时间试图选择一个“正确”的 shell 更重要。


.. hint::
    
    hint 精通正在使用的 shell 远比花大量时间试图选择一个“正确”的 shell 更重要。




变量
************************************

变量是一个用来存储数据的实体，由一个变量名和值组成。在 Shell 中有两种不同类型的变量，分别是 Shell 变量和环境变量，也被称为局部变量和全局变量。

其实在 Bash 中只允许创建局部变量，也就是说每个新变量都会自动设置成局部变量。如果希望某个变量成为环境变量，必须使用 export 命令将局部变量修改为“局部+全局”变量，这可以称为将变量导出到环境中。Bash 处理变量的这种混乱形式是为了保持向后兼容，从 Bourne Shell 中继承过来的一种设计缺陷。

.. attention ::
    
    在 Linux 中启动一个 Shell 会创建一个进程，修改环境变量后只会对该进程及子进程有效，不会传递到父进程中。要使环境变量对所有的 Shell 起作用需要修改配置文件。


Shell 环境中包含很多的变量。虽然 Shell 环境变量根据发行版本的不同而不同，但是一般都会包含以下的环境变量：

==========   ==========
变量名        说明
==========   ==========
SHELL         Shell 程序的名字
HOME          用户的家目录
LANG          系统语言及字符集
PAGER         页输出程序的名字。这经常设置为/usr/bin/less。
PATH          系统查找命令的路径（由冒号分开的目录列表）
PS1           Shell 提示符
PWD           当前工作目录。
TERM          终端类型（终端仿真器所用的协议）
USER          当前用户名
==========   ==========

可以使用 printenv 和 env 命令查看 Shell 中的环境变量。

- :doc:
修改 Shell 提示符
修改 PATH 命令搜索路径
set 命令可以 显示 shell 或环境变量，而 printenv 只是显示环境变量。

选项
************************************

由于 Shell 的特殊性，除了在启动程序时添加选项外，还可以在程序运行时设置选项。即使用 set 命令的一种变体， ``set -o option`` 打开/关闭选项。

::

    $ set -o
    allexport      	off
    braceexpand    	on
    emacs          	on
    errexit        	off
    errtrace       	off
    functrace      	off
    hashall        	on
    histexpand     	on
    history        	on
    ignoreeof      	on
    interactive-comments	on
    keyword        	off
    monitor        	on
    noclobber      	off
    noexec         	off
    noglob         	off
    nolog          	off
    notify         	off
    nounset        	off
    onecmd         	off
    physical       	off
    pipefail       	off
    posix          	off
    privileged     	off
    verbose        	off
    vi             	off
    xtrace         	off

下边解释几个常用的选项：

===================     ====================
选项                     含义
===================     ====================
emacs                    命令行编辑器： Emacs 模式，关闭 vi 模式
vi                       命令行编辑器： vi 模式，关闭 Emacs 模式
history                  历史命令列表
ignoreeof                忽略 eof 信号，即忽略 <Ctrl+D> 退出命令
monitor                  后台作业控制
noclobber                是否允许重定向的输出替换文件
===================     ====================


元字符
************************************

元字符（Meta Character）是指在 Shell 中具有特殊含义的字符，因为元字符是被 Shell 解释的，所以不同的 Shell 环境中元字符不一定完全相同。

简单的讲：元字符就是一些定义为特殊意义的字符。最常用的元字符如： ``~`` 表示
home 目录， ``&`` 在后台运行程序， ``\\`` 转移字符等。


:doc:`metacharacter`


历史列表
************************************

在输入命令时，Shell 会将每条命令保存到历史列表中。可以使用不同的方式访问历史列表、调取历史命令或者对历史命令进行二次修改执行。简单的可以用 <Up> 、 <Down> 键调取上一条或下一条命令。

在历史列表中，每一条命令称为一个事件，而每个事件都有一个内部编号，称为事件编号。历史列表的功能就是它可以基于事件编号调取命令。例如用 ``!24`` 重新执行编号为 24 的命令。


.. attention::

    每条执行过的命令都会添加到历史列表中，包括错误的命令以及 ``history`` 命令本身。

可以设置 HISTSIZE 环境变量来指定历史列表的大小，即历史列表中可以存放历史命令的条目。

::

    [Linux]$ export HISTSIZE=1000

Shell 中还有几个好用的历史列表命令，如 ``!!`` 执行上一个命令，``!*`` 使用上一条命令的选项和参数， ``!$`` 使用上一个命令的参数。

::
    [Linux]$ ls -l Music/
    total 0
    [Linux]$ !!
    ls -l Music/
    total 0
    [Linux]$ ls !*
    ls -l Music/
    total 0
    [Linux]$ ls !$
    ls Music/


:doc:`../Chapter01/00_history`


自动补全
************************************

Shell 中可以使用 <Tab> 键自动补全命令、路径及文件名、环境变量等。如果有多个选择时，再次按 <Tab> 键，会显示所有可能匹配的文件列表。如果不能自动补全单词，那么 Shell 将发出嘀嘀声。

通常，自动补全有 5 种类型：

================    ================
自动补全             补全对象
================    ================
文件名补全           路径及文件名
命令补全             内部及外部命令
变量补全             变量
用户名补全           系统上的用户名
主机名补全           局域网上的计算机
================    ================

::

    # 命令补全，输入以下字符然后按 <Tab> 键，将自动补全 whoami
    [Linux] $ whoa

    # 变量补全，必须以 $ 符号开头，输入以下字符然后按<Tab>键
    [Linux] $ echo $H
    $HISTCMD       $HISTFILE      $HISTSIZE      $HOSTNAME      
    $HISTCONTROL   $HISTFILESIZE  $HOME          $HOSTTYPE

    # 用户名补全，必须以 ~ 符号开头，输入以下字符然后按 <Tab> 键
    [Linux] $ echo ~gle

    # 主机名补全，必须以 @ 符号开头，输入以下字符然后按 <Tab> 键
    [Linux] $ echo @gle


别名
************************************

别名就是赋予一条命令或者一组命令的名称。可以将别名看作是缩写，或者自定义命令的变体。创建别名需要使用 ``alias`` 命令：

::

    [Linux] $ alias info='date; who'

也可以不加选项和参数查看系统中已经定义的别名。

::

    alias egrep='egrep --color=auto'
    alias fgrep='fgrep --color=auto'
    alias grep='grep --color=auto'
    alias l='ls -CF'
    alias la='ls -A'
    alias ll='ls -alF'
    alias ls='ls --color=auto'
    alias vi='vim

:doc:`../Chapter01/00_alias`


内置命令
************************************

Shell 有很多内置在其源代码中的命令。这些命令是内置的，所以 Shell 不必到磁盘上搜索它们，执行速度因此加快。不同的 Shell 内置命令有所不同。

Bash 常用的内置命令

alias： 显示和创建已有命令的别名。
bg： 把作业放到后台。
cd [arg]： 改变目录，如果不带参数，则回到主目录，带参数则切换到参数所指的目录。
disown： 从作业表中删除一个活动作业。
echo [args]： 显示 args 并换行。
eval [args]： 把 args 读入 Shell，并执行产生的命令。
exec command： 运行命令，替换掉当前 Shell。
exit [n]： 以状态 n 退出 Shell。
export [var]：使变量可被子 Shell 识别。
fc： 历史的修改命令，用于编辑历史命令。
fg： 把后台作业放到前台。
getopts： 解析并处理命令行选项。
help [command]： 显示关于内置命令的有用信息。如果指定了一个命令，则将显示该命令的详细信息。
history： 显示带行号的命令历史列表。
jobs： 显示放到后台的作业。
kill [-signal process]： 向由 PID 号或作业号指定的进程发送信号。
logout： 退出登录 Shell。
pwd： 打印出当前的工作目录。
read [var]： 从标准输入读取一行，保存到变量 var 中。
set： 设置选项和位置参量。
stop pid： 暂停第 pid 号进程的运行。
suspend： 终止当前 Shell 的运行（对登录 Shell 无效）。
times： 显示由当前 Shell 启动的进程运行所累计用户时间和系统时间。
type [command]： 显示命令的类型，例如：pwd 是 Shell 的一个内置命令。
unalias： 取消所有的命令别名设置。
wait [pid#n]： 等待 pid 号为 n 的后台进程结束，并报告它的结束状态。

.. attention::

    编写 Shell 脚本时，可以使用特殊的内置命令 for、if、while 等来控制脚本流程，这些命令有时候称为关键字。
