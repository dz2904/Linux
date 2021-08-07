.. _cmd_chgrp:

chgrp 改变文件所属组
####################################

chgrp 命令用于改变文件或目录的所属组，它允许普通用户改变文件所属的组，只要该用户是该组的一员。


命令格式：
************************************

.. highlight:: none

::

    chgrp [OPTION]... GROUP FILE...
    chgrp [OPTION]... --reference=RFILE FILE...

    
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

    # 改变文件所属组
    [Linux]# chgrp root a.py
    [Linux]# ls -l
    -rwxr-xr-x 1 xiao root  105 Jul  2 20:59 a.py

    # 指定组 ID 
    [Linux]# chgrp 1001 a.py
    [Linux]# ls -l
    -rwxr-xr-x 1 xiao geroge  105 Jul  2 20:50 a.py
