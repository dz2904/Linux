.. _cmd_cat:

cat 查看和连接文件内容
####################################

cat 命令用于连接文件并打印到标准输出设备上。

命令格式：
************************************

.. highlight:: none

::

    cat [OPTION]... [FILE]...

    
常用选项：
************************************

::

    -b, --number-nonblank
        和 -n 相似，只不过对于空白行不编号。

    -n, --number
        由 1 开始对所有输出的行数编号

    -s, --squeeze-blank
        当遇到有连续两行以上的空白行，就代换为一行的空白行


使用实例：
************************************

::

    [Linux]$ cat a.sh 
    #! /usr/bin/bash
    echo "Hello world !"


    [Linux]$ cat a.sh b.sh 
    #! /usr/bin/bash
    echo "Hello world !"
    #! /usr/bin/bash
    name="Linus Benedict Torvalds"
    echo $name


    [Linux]$ cat -n a.sh b.sh 
         1	#! /usr/bin/bash
         2	echo "Hello world !"
         3	#! /usr/bin/bash
         4	name="Linus Benedict Torvalds"
         5	echo $name

    # 利用重定向功能，另存为拼接文件
    [Linux]$ cat -n a.sh b.sh > all.sh

