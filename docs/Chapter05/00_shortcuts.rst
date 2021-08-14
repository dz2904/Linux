Shell 快捷键
####################################

命令行用户在使用命令行时并不喜欢敲入太多文本，所以命令中才会有 cp、ls、mv 和 rm 那么多缩写的命令名。命令行最大的目标之一就是减少操作（省事），用最少的击键次数执行最多的任务。为了提高命令行下的工作效率，使用一些快捷键是提高效率的最简单也是最直接方式。

Bash 使用了一个名为 Readline 的库（供不同的应用程序共享使用的线程集合），来实现命令行编辑。例如：用箭头键来移动光标，此外还有许多特性。

.. highlight:: none

.. attention::

    Shell 中有两种命令行编辑的模式，即 vi 模式和 Emacs 模式（Bash 默认使用的是 Emacs 模式）。在两种模式中使用不同的快捷键操纵在命令行中键入的内容，包括使用历史列表和自动补全的功能。

    如果快捷键不能使用，可以将命令行编辑模式设置为 Emacs 默认值。

    ::

        [Linux]$ set -o emacs


控制 Shell 进程
************************************

==========   ==========
按键         作用
==========   ==========
<Ctrl-S>     挂起终端（有点像卡住了，但输入的命令还会执行）
<Ctrl-Q>     恢复挂起的终端（恢复输入状态，并输出挂起时执行的命令）
<Ctrl-C>     终止前台命令的执行
<Ctrl-D>     发送表示标准输入结束的 EOF 信号。经常用于退出程序或 Shell。
<Ctrl-Z>     暂停前台命令的执行，并放入后台保存
==========   ==========


移动光标
************************************

==========   ==========
按键         作用
==========   ==========
<Ctrl-P>     上一条命令（相当于 <Up>）
<Ctrl-N>     下一条命令（相当于 <Down>）
<Ctrl-B>     光标左移一个字符（相当于 <Left>）
<Ctrl-F>     光标右移一个字符（相当于 <Right>）
<Ctrl-A>     移动光标到行首
<Ctrl-E>     移动光标到行尾
==========   ==========


剪切和粘贴
************************************

Readline 的文档使用术语 killing 和 yanking 来指代我们平常所说的剪切和粘贴。剪切的本文存储在一个叫做剪切环（kill-ring）的缓冲区中。

==========   ==========
按键         作用
==========   ==========
<Ctrl-H>     删除一个字符（相当于 <Backspace>）
<Ctrl-U>     剪切从光标处到行首的字符
<Ctrl-K>     剪切从光标处到行尾的字符
<Ctrl-W>     剪切从光标处到词尾的字符
<Ctrl-Y>     粘贴文本到光标处
<Alt-R>      清空行（如果命令是从历史列表中复制的，则会恢复到原始命令）
==========   ==========


其它常用功能
************************************

==========   ==========
按键         作用
==========   ==========
<Ctrl-L>     清空屏幕（相当于 ``clear`` 命令）
<Ctrl-R>     反向递增搜索历史列表中的命令
<Ctrl-J>     换行符（相当于 <Enter> ）
<Ctrl-M>     返回符（相当于 <Enter> ）
<Ctrl-V>     插入控制字符
<Ctrl-T>     互换光标处和光标前面的字符位置
==========   ==========


.. hint ::

    在开发 Unix 时因为成本的原因，使用了电传打字机作为终端。在电传打字机上执行回车时，包含两种操作，首先将托盘返回到最左边的位置上（即 CR 码返回信号），然后将打印纸向上移动一行（即 LF 码换行信号）。

    在终端快捷键中，<Ctrl-M> 发送返回信号，<Ctrl-J> 发送换行信号。在终端执行命令时，软件会把 CR 和 LF 信号转换为 CR+LF 回车信号。


自定义快捷键
************************************

使用 ``bind`` 命令可以自定义快捷键，Bash 中的快捷键其实是 Readline 来提供的，因此，这里快捷键的设置其实就是配置 Readline，Readline 中分两种快捷键，一种是 Readline 内部的函数快捷键，另外一种是执行 Shell 命令，设置的时候稍有不同：

::

    # 查看 Readline 中可以使用的函数名称
    bind -l

    # 已经绑定的快捷键
    bind -p

    # 自定义快捷键
    # 绑定后，按[C-x,C-L]就能执行ls -al
    bind -x '"/C-x/C-l":ls -al'


这种设置只针对当前的会话有效，一旦会话丢失，设置的快捷键就会丢失。为了让设置的快捷键永久有效，需要编辑配置文件，在 Linux 系统中，有两个配置文件（全局的和用户的），全局的配置文件是 ``/etc/inputrc`` ，而用户的配置文件在家目录下 ``~/.inputrc`` 。inputrc 文件的大概样子像下面这样：

::

    # 本例来自 CentOS6.4 
    $if mode=emacs

    # for linux console and RH/Debian xterm
    "/e[1~": beginning-of-line
    "/e[4~": end-of-line
    # commented out keymappings for pgup/pgdown to reach begin/end of history
    #"/e[5~": beginning-of-history
    #"/e[6~": end-of-history
    "/e[5~": history-search-backward
    "/e[6~": history-search-forward
    "/e[3~": delete-char
    "/e[2~": quoted-insert
    "/e[5C": forward-word
    "/e[5D": backward-word
    "/e[1;5C": forward-word
    "/e[1;5D": backward-word

    # for rxvt
    "/e[8~": end-of-line
    "/eOc": forward-word
    "/eOd": backward-word

    # for non RH/Debian xterm, can't hurt for RH/DEbian xterm
    "/eOH": beginning-of-line
    "/eOF": end-of-line

    # for freebsd console
    "/e[H": beginning-of-line
    "/e[F": end-of-line
    $endif


在配置文件中，``/C`` 代表 <Ctrl> 键，``/M`` 代表 <Alt> 键，``/e`` 代表 <Esc> 键，``//`` 代表反斜杠，``/'`` 代表单引号，``/"`` 代表双引号；

可以通过 <CTRL-V> 来查看某一个功能键的字符序列，或者输入 cat 后回车，进入编辑中，直接按快捷键。配置文件中可能会使用八进制或者十六进制来表示字符。

