pwd 显示工作目录
##########################

pwd 命令是 Linux 中最常用的命令之一，它用于显示当前工作路径。

命令格式：
***********************

.. highlight:: none

::

    pwd [-L | -P]

常用选项：
***********************

::

    -L
      如果目录是一个符号连接，则直接显示符号连接路径，而非符号连接所指向的路径（默认值）

    -P
      如果目录是一个符号连接，则直接显示连接文件所指向的文件路径

使用实例：
***********************

::

    # 显示当前目录
    [root@localhost ~]# cd /
    [root@localhost /]# pwd
    /

    [root@localhost /]# ls -l
    total 20
    lrwxrwxrwx.   1 root root    7 Mar 27  2018 bin -> usr/bin
    ...
    [root@localhost /]# cd lib

    # 显示符号连接路径（默认值）
    [root@localhost lib]# pwd -L
    /lib

    # 显示符号连接所指向的路径
    [root@localhost lib]# pwd -P
    /usr/lib
