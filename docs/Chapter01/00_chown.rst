.. _cmd_chown:

chown 改变文件属主
####################################

Linux 是多用户操作系统，所有的文件都有拥有者。chown 命令用于改变文件或目录的所有者或所属的组。


.. attention::

    chown 需要用到多个用户权限，只有 root 权限才能执行此命令。


命令格式：
************************************

.. highlight:: none

::

    chown [OPTION]... [OWNER][:[GROUP]] FILE...
    chown [OPTION]... --reference=RFILE FILE...

    
常用选项：
************************************

::

    -f, --silent, --quiet
        不显示错误信息

    -v, --verbose
        显示命令执行过程

    -h, --no-dereference
        只修改符号连接的文件，不更改其他相关文件

    -R, --recursive
        递归处理，将目录下的所有文件及子目录一并处理


使用实例：
************************************

::

    [Linux]$ ls -l
    -rwxr-xr-x 1 xiao xiao  105 Jul  2 20:50 a.py

    # 改变文件所有者
    [Linux]# chown root a.py
    [Linux]# ls -l
    -rwxr-xr-x 1 root xiao  105 Jul  2 20:59 a.py

    # 改变文件所有者和所属组
    [Linux]# chown root:root a.py
    [Linux]# ls -l
    -rwxr-xr-x 1 root root  105 Jul  2 21:30 a.py

    # 指定用户 ID
    [Linux]# chown 1000 a.py
    [Linux]# ls -l
    -rwxr-xr-x 1 xiao root  105 Jul  2 20:50 a.py
    
    # 指定所属组 ID
    [Linux]# chown :1000 a.py
    [Linux]# ls -l
    -rwxr-xr-x 1 xiao xiao  105 Jul  2 20:50 a.py
