新建文件与文件夹
###########################

- touch_ 新建文件

- mkdir_ 新建文件夹

.. _touch:

touch
*****************************

touch命令有两个功能：

1. 更新文件的时间标签（文件内容不会改动）；

2. 创建新的空文件。

touch 命令详解
==============================

命令格式：

.. highlight:: none

::

    touch [OPTION]... FILE...


选项：

=========    ==================    ==============
选项            长选项                说明
=========    ==================    ==============
-a                                  只更新访问时间
-c            --no-create           不创建任何文件
-d            --date=STRING         指定更新的时间（STRING 时间格式）
-m                                  只更新修改时间
-r            --reference=FILE      按指定文件的时间更新时间
-t STAMP                            指定更新的时间（[[CC]YY]MMDDhhmm[.ss]格式）
=========    ==================    ==============

实例
==============================

**新建文件** 在当前路径下不存在 book.md ：

::

    [root@linuxtechi ~]# touch book.md


**新建文件** 在 /media 路径下：

::

    [root@linuxtechi ~]# touch /media/book.md

mkdir
*******************************

mkdir 是 “make directory” 的缩写。mkdir 是用来创建文件夹的命令，此命令属于内建命令。

.. note::

    新建文件夹之前，必须对目标路径具有写和执行的权限。并且不能与路径下的文件夹重名。  

mkdir 命令详解
=============================

命令格式：

::

    mkdir [OPTION]... DIRECTORY...


选项：

=====    ================    =============
选项       长选项               说明
=====    ================    =============
-m        --mode=MODE         新建文件夹的同时设置权限
-p        --parents           按路径递归的新建文件夹
-v        --verbose           显示详细的执行过程
-Z        --context=CTX       将新建文件夹的 SELinux 安全环境设置为 CTX
=====    ================    =============

实例
===============================

**新建文件夹** 默认会在当前路径下新建文件夹：

::

    [root@linuxtechi ~]# mdkir office


**新建文件夹** 在 /media 路径下：

::

    [root@linuxtechi ~]# mdkir /media/office


**新建多个文件夹**：

::

    [root@linuxtechi ~]# mdkir office code gitbook


**递归的新建文件夹**：

::

    [root@linuxtechi ~]# mdkir -p office/code


**新建权限为 750 的文件夹**：

::

    [root@linuxtechi ~]# mdkir -m 750 office

