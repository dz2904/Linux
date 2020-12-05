mkdir 创建新文件夹
####################################

mkdir 是 “make directory” 的缩写。mkdir 是用来创建文件夹的命令，此命令属于内建命令。

.. tips::

    新建文件夹之前，必须对目标路径具有写和执行的权限，并且不能与路径下的文件夹重名。

命令格式：
************************************

.. highlight:: none

::

    mkdir [OPTION]... DIRECTORY...

常用选项：
************************************

::

    -m, --mode=MODE
      指定文件夹权限（如在 chmod 中），而不是 a=rwx-umask

    -p, --parents
      按路径递归的新建文件夹

     -v, --verbose
      显示详细的执行过程


使用实例：
************************************

::

    # 新建文件夹
    [root@localhost bin]# ls
    bbb.py  usr
    [root@localhost bin]# mkdir abc
    [root@localhost bin]# ls -F
    abc/  bbb.py  usr/

    # 递归新建文件夹
    [root@localhost bin]# mkdir -p abc/aaa/bbb/ccc
    [root@localhost bin]# ls -R
    .:
    abc  bbb.py  usr

    ./abc:
    aaa

    ./abc/aaa:
    bbb

    ./abc/aaa/bbb:
    ccc

    ./abc/aaa/bbb/ccc:


    # 新建指定权限的文件夹
    [root@localhost bin]# mkdir -m 750 office
    [root@localhost bin]# ls -l
    total 0
    drwxr-x--- 2 root root  6 Feb 15 18:54 office
    ...
