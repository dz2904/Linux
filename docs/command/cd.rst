cd 切换目录
##########################

cd 命令是 Linux 中最常用的命令之一，它用于切换目录。

命令格式：
***********************

.. highlight:: none

::

    cd [directory]

常用选项：
***********************

::

    -L
      如果目标目录是一个符号连接，则直接切换到符号连接目录，而非符号连接所指向的目标目录（默认值）

    -P
      如果目标目录是一个符号连接，则直接切换到符号连接指向的目标目录


使用实例：
***********************

::

    # 切换到主目录
    [root@localhost ~]# cd
    [root@localhost ~]# pwd
    /root
    [root@localhost /]# cd ~
    [root@localhost ~]# pwd
    /root

    # 切换到根目录
    [root@localhost ~]# cd /
    [root@localhost /]# pwd
    /

    # 切换到 /etc 目录
    [root@localhost ~]# cd /etc/
    [root@localhost etc]# pwd
    /etc

    # 快速切换到上一个工作目录
    [root@localhost etc]# cd -
    /root
    [root@localhost ~]# pwd
    /root

    # 切换到根目录
    [root@localhost ~]# cd /
    [root@localhost /]# ls -l
    total 20
    lrwxrwxrwx.   1 root root    7 Mar 27  2018 bin -> usr/bin
    ...

    # 切换到符号连接指向的目标目录
    [root@localhost /]# cd -P lib
    [root@localhost lib]# pwd
    /usr/lib

    # 切换到符号连接目录（默认值）
    [root@localhost lib]# cd /
    [root@localhost /]# cd -L lib
    [root@localhost lib]# pwd
    /lib
