.. _cmd_whereis:

whereis 搜索命令文件
####################################

whereis 命令可以查找指定命令的二进制文件、源文件和帮助文件。 被找到的文件在显示时，会去掉主路径名，然后再去掉文件的单个尾部扩展名（如: ``.c`` ），源于源代码控制的 ``s.`` 前缀也会被去掉。接下来，whereis 会尝试在标准的 Linux 位置里寻找具体程序，也会在由 ``$PATH`` 和 ``$MANPATH`` 指定的路径中寻找。


命令格式：
************************************

.. highlight:: none

::

        whereis [options] [-BMS directory... -f] name...


常用选项：
************************************

::

    -b 只搜索二进制文件
    -B <目录> 只在路径下查找二进制文件
    -m 只搜索man手册
    -M <目录> 只在路径下查找man手册
    -s 只搜索源码文件
    -S <目录> 只在指定路径下查找源码文件
    -f 终止目录列表和文件开头信号（其实就是结尾符号）。使用-B，-M，-S选项时，必须使用。
    -u 仅显示具有异常项的命令名。
    -l 输出有效查找路径


使用实例：
************************************

::

    # 寻找二进制文件所在位置
    [Linux]$  whereis ls
    ls: /usr/bin/ls /usr/share/man/man1/ls.1.gz
    
    # 只搜索二进制文件
    [Linux]$  whereis -b ls
    ls: /usr/bin/ls
    
    # 指定多个搜索位置
    # -f 为必须项，用来明确分隔目录列表和要搜索的文件名。
    [Linux]$  whereis -B /usr/bin/ -f ls
    ls: /usr/share/man/man1/ls.1.gz /usr/bin/ls

    # 查看搜索路径
    [Linux]$  whereis -l
    bin: /usr/bin
    bin: /usr/sbin
    bin: /usr/lib/x86_64-linux-gnu
    bin: /usr/lib
    bin: /usr/lib64
    bin: /etc
    bin: /usr/games
    bin: /usr/local/bin
    bin: /usr/local/sbin
    bin: /usr/local/etc
    bin: /usr/local/lib
    bin: /usr/local/games
    bin: /usr/include
    bin: /usr/local
    bin: /usr/libexec
    bin: /usr/share
    bin: /snap/bin
    man: /usr/share/man/sk
    man: /usr/share/man/fi
    ......


