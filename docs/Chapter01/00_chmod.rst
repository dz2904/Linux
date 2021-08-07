.. _cmd_chmod:

chmod 变更权限
####################################

chmod 命令用来变更文件或目录的权限。

:doc:`权限 <../Chapter03/00_permission>` 范围的表示法如下：

- u User 文件或目录的拥有者
- g Group 文件或目录的所属群组
- o Other 除了文件或目录的拥有者和所属组之外的其他用户
- a All 全部的用户
- r 读取权限，数字代号“4”
- w 写入权限，数字代号“2”；
- x 执行或切换权限，数字代号“1”
- \- 不具任何权限，数字代号为“0”
- s 强制位权限，使其他用户临时拥有文件拥有者的权限
- t 粘滞位权限，用户只能删除自己为属主的文件

.. attention ::

    符号连接的权限无法变更，对符号连接修改权限实际会改变原始文件的权限。


命令格式：
************************************

.. highlight:: none

::

    chmod [OPTION]... MODE[,MODE]... FILE...
    chmod [OPTION]... OCTAL-MODE FILE...


修改权限有两种表达方式，即符号模式和八进制模式。

使用符号模式可以增加、删除或直接指定权限，每个项目的设置可以用逗号隔开。

======   ======
符号      说明
======   ======
\+ 	      增加指定的权限
\- 	      去除指定的权限
\=        设置指定的权限
======   ======

使用八进制数来指定权限。

======   ======   ======
代码      rwx      权限
======   ======   ======
7         rwx      读 + 写 + 执行
6         rw-      读 + 写
5         r-x      读 + 执行
4         r--      只读
3         -wx      写 + 执行
2         -w-      只写
1         --x      只执行
0         ---      无
======   ======   ======


常用选项：
************************************

::

    -c, --changes
        效果类似“-v”参数，但仅显示更改的部分

    -f, --silent, --quiet
        不显示错误信息

    -v, --verbose
        显示执行过程

    -R, --recursive
        递归处理，将指令目录下的所有文件及子目录一并处理


使用实例：
************************************

::


    [Linux]$ ls -l
    -rwxr-xr-x 1 xiao xiao   42 Jul 28 10:40 a.sh

    # 删除 other 的执行权限
    [Linux]$ chmod o-x a.sh
    [Linux]$ ls -l
    -rwxr-xr-- 1 xiao xiao   42 Jul 28 10:40 a.sh

    # 针对属主和属组删除执行权限
    [Linux]$ chmod ug-x a.sh
    [Linux]$ ls -l
    -rw-r--r-- 1 xiao xiao   42 Jul 28 10:40 a.sh

    # 分别设置权限
    [Linux]$ chmod u=rwx,g=rx,o=r a.sh 
    [Linux]$ ls -l
    -rwxr-xr-- 1 xiao xiao   42 Jul 28 10:40 a.sh

    # 使用八进制方式设置权限
    [Linux]$ chmod 654 a.sh
    [Linux]$ ls -l
    -rw-r-xr-- 1 xiao xiao   42 Jul 28 10:40 a.sh

    # 设置特殊权限
    [Linux]$ chmod o+t abc
    [Linux]$ ls -l
    drwxr-xr-t 2 xiao xiao 4096 Aug  6 15:23 abc
