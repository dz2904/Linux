touch 创建新文件
##########################

touch 命令有两个功能：

1. 更新一个或多个文件的时间标签（文件内容不会改动）；

2. 创建一个或多个新的空文件。

命令格式：
***********************

.. highlight:: none

::

    touch [OPTION]... FILE...

常用选项：
***********************

::

    -a
      只更改存取时间

    -d, --date=STRING
      使用指定的日期时间，而非现在的时间

    -m
      只更该变动时间

    -r, --reference=FILE
      按指定文件的时间更新时间

    -t STAMP
      使用 [[CC]YY]MMDDhhmm[.ss] 格式的时间日期，而不是当前的时间

    --version
      显示版本信息

使用实例：
***********************

::

    # 新建文件
    [root@localhost bin]# ls
    aaa  usr
    [root@localhost bin]# touch bbb.py
    [root@localhost bin]# ls
    aaa  bbb.py  usr

    # 在指定路径下新建文件
    [root@localhost bin]# ls usr/
    bin
    [root@localhost bin]# touch usr/ccc.py
    [root@localhost bin]# ls usr/
    bin  ccc.py
