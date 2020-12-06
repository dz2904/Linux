重新认识 Shell
####################################

从刚刚开始使用 Linux 时，就会有很多 Shell 的话题，那么 Shell 到底是什么呢？

Shell 是一个应用程序，充当用户界面和脚本解释器，是用户使用 Linux 系统的桥梁。它即是一个程序也是一种脚本语言。

因为 Shell 是一个程序，所以在 Linux 环境中有众多的 shell 程序，但是它们主要分为两大家族（Shell 家族之间的差异会比较大，同一家族的差异相对较小）：

- Bourne Shell 家族： sh、ksh、bash
- C-Shell 家族： csh、tcsh

因为 Linux 系统中大部分默认使用 Bash，所以们主要以它主，介绍一下几个方面。


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


推荐阅读： :doc:`../Chapter02/11_shell.ps1` 、 :doc:`../Chapter01/00_env`

修改 PATH 命令搜索路径


选项
************************************

由于 Shell 的特殊性，除了在启动程序时添加选项外，还可以在程序运行时设置选项。即使用 set 命令的一种变体， ``set -o option`` 打开/关闭选项。

.. highlight:: none

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


推荐阅读： :doc:`metacharacter`


历史列表
************************************

在输入命令时，Shell 会将每条命令保存到历史列表中。可以使用不同的方式访问历史列表、调取历史命令或者对历史命令进行二次修改执行。简单的可以用 <Up> 、 <Down> 键调取上一条或下一条命令。

在历史列表中，每一条命令称为一个事件，而每个事件都有一个内部编号，称为事件编号。历史列表的功能就是它可以基于事件编号调取命令。例如用 ``!24`` 重新执行编号为 24 的命令。


.. attention::

    每条执行过的命令都会添加到历史列表中，包括错误的命令以及 :ref:`history <cmd_history>` 命令本身。

可以设置 HISTSIZE 环境变量来指定历史列表的大小，即历史列表中可以存放历史命令的条目。

::

    [Linux]$ export HISTSIZE=1000


推荐阅读： :doc:`../Chapter01/00_history`


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
    # 主机名自动补全只会补全包含在 /etc/hosts 文件中的主机名
    [Linux] $ echo @gle


.. note::

    在 Bash 新版本中有一个叫做“可编程自动补全”工具。可编程自动补全允许用户（更可能是系统发行版提供商）添加额外的自动补全规则。一般来说，这样做是为了支持特定的应用，例如，可以为一个命令的长选项，添加自动补全。在 Ubuntu 发行版中定义了一个相当大的规则集合，可编程自动补全是通过 Shell 函数来实现的。

别名
************************************

别名允许用户只输入一个单词就运行任意一个命令或一组命令（包括命令选项和文件名）。可以将别名看作是命令的快捷方式（就像是软链接），也可以将别名看作是缩写。使用别名可以在命令行中减少输入的时间，使工作更流畅，同时增加生产率。

:ref:`alias <cmd_alias>` 命令用于创建临时的别名，在设置别名后，只在当前登录会话中有效。如果退出 Shell 或重启系统后，别名就会消失。如果想让别名永久生效，可以将别名定义写入配置文件 ``~/.bashrc`` 中。不加选项和参数执行 :ref:`alias <cmd_alias>`  命令会显示所有已定义的别名列表。 :ref:`unalias <cmd_unalias>`  命令用于删除别名。

::

    # 定义别名
    [Linux] $ alias info='date; who'

    # 查看系统中的别名
    [Linux] $ alias 
    alias info='data; who'
    alias la='ls -A'
    alias ll='ls -alF'
    alias ls='ls --color=auto'
    alias vi='vim

    # 删除别名
    [Linux] $ unalias info



别名的日常用法总结：

1. 为命令设置默认的参数（例如 alias ping='ping -c 5' 设置 ping 命令的次数，alias rm='rm -i' 删除文件时需要确认）。
2. 设置系统中多版本命令的默认路径（例如 GNU/grep 位于 /usr/local/bin/grep 中而 Unix grep 位于 /bin/grep 中。若想默认使用 GNU grep 则设置别名 grep='/usr/local/bin/grep' )。
3. 为跨平台的操作创建命令别名，以增加兼容性（比如 alias ipconfig=ifconfig）。


推荐阅读： :doc:`../Chapter01/00_alias`


内置命令
************************************

Shell 有很多内置在其源代码中的命令。这些命令是内置的，所以 Shell 不必到磁盘上搜索它们，执行速度因此加快。不同的 Shell 内置命令有所不同。

Bash 常用的内置命令

- :ref:`alias <cmd_alias>` ：显示和创建已有命令的别名。
- :ref:`bg <cmd_bg>` ：把作业放到后台。
- :ref:`cd [arg] <cmd_cd>` ：改变目录，如果不带参数，则回到主目录，带参数则切换到参数所指的目录。
- :ref:`disown <cmd_disown>` ：从作业表中删除一个活动作业。
- :ref:`echo [args] <cmd_echo>` ：显示 args 并换行。
- :ref:`eval [args] <cmd_eval>` ：把 args 读入 Shell，并执行产生的命令。
- :ref:`exec command <cmd_exec>` ：运行命令，替换掉当前 Shell。
- :ref:`exit [n] <cmd_exit>` ：以状态 n 退出 Shell。
- :ref:`export [var] <cmd_export>` ：使变量可被子 Shell 识别。
- :ref:`fc <cmd_fc>` ：历史的修改命令，用于编辑历史命令。
- :ref:`fg <cmd_fg>` ：把后台作业放到前台。
- :ref:`getopts <cmd_getopts>` ：解析并处理命令行选项。
- :ref:`help [command] <cmd_help>` ：显示关于内置命令的有用信息。如果指定了一个命令，则将显示该命令的详细信息。
- :ref:`history <cmd_history>` ：显示带行号的命令历史列表。
- :ref:`jobs <cmd_jobs>` ：显示放到后台的作业。
- :ref:`kill [-signal process] <cmd_kill>` ：向由 PID 号或作业号指定的进程发送信号。
- :ref:`logout <cmd_logout>` ：退出登录 Shell。
- :ref:`pwd <cmd_pwd>` ：打印出当前的工作目录。
- :ref:`read [var] <cmd_read>` ：从标准输入读取一行，保存到变量 var 中。
- :ref:`set <cmd_set>` ：设置选项和位置参量。
- :ref:`stop pid <cmd_pid>` ：暂停第 pid 号进程的运行。
- :ref:`suspend <cmd_suspend>` ：终止当前 Shell 的运行（对登录 Shell 无效）。
- :ref:`times <cmd_times>` ：显示由当前 Shell 启动的进程运行所累计用户时间和系统时间。
- :ref:`type [command] <cmd_type>` ：显示命令的类型，例如：pwd 是 Shell 的一个内置命令。
- :ref:`unalias <cmd_unalias>` ：取消所有的命令别名设置。
- :ref:`wait [pid#n] <cmd_wait>` ：等待 pid 号为 n 的后台进程结束，并报告它的结束状态。

.. attention::

    编写 Shell 脚本时，可以使用特殊的内置命令 for、if、while 等来控制脚本流程，这些命令有时候称为关键字。


配置文件
************************************

Bash 允许自定义工作环境，其中包含两类：初始化文件和注销文件，其中初始化文件又分为登陆文件和环境文件。

+------------------------------+----------------+
| 初始化文件                   | 注销文件       |
+===============+==============+================+
| 登陆文件      | 环境文件     |                |
+---------------+--------------+                |
| .bash_profile | .bashrc      | .bashrc_logout |
| .bash_login   |              |                |
+---------------+--------------+----------------+

当用户登录系统时，会加载初始化文件，设置或清空一系列变量，有时还会执行一些自定义的命令。

Bash 的初始化文件按范围划分，可以分为两类：

- 全局配置文件：

    - /etc/profil
    - /etc/profile.d/\*.sh （需要读权限）
    - /etc/bashrc

- 个人配置文件：

    - ~/.bash_profile
    - ~/.bashrc

按功能划分，也可以分为两类：

- profile 类：为交互式 Shell 提供配置，用于定义环境变量、用于运行命令或脚本。profile 里面的内容，在系统登录后执行。

- bashrc 类：为非交互式和交互式 Shell 提供配置，经常用于初始化文件，定义命令别名、定义本地变量。bashrc 在登录 Shell 时会自动执行。


.. hint ::

    Shell 可以分为交互式和非交互式两种，当正常登陆系统时启动的是交互式的 Shell，当运行脚本时启动的是非交互式的 Shell。启动不同类型的 Shell 在加载配置文件时，会有细微的差别。

