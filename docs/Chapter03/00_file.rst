一切皆文件
####################################

“一切皆是文件”是 Unix/Linux 的基本哲学之一，它是指 Linux 系统中的所有的一切都可以通过文件的方式访问、管理，即使不是文件，也以文件的形式来管理。例如硬件设备、进程、套接字等都抽象成文件，使用统一的用户接口，虽然文件类型各不相同，但是对其提供的却是同一套操作。

这里的一切是单向的，也即所有的东西都单向通过文件系统呈现，反向不一定可行。比如通过新建文件的方式来创建磁盘设备是行不通的。

.. hint:: 不准确但是形象的例子

    Linux 系统把硬件设备映射成文件，例如将摄像头映射为 /dev/video，然后就可以使用基本的函数操作它。用 open() 函数连接设备，再用 read() 函数读取图像，最后用 write() 函数保存图像。

    而在声卡设备中，read() 函数会变为录音功能，write() 函数变为播放功能。


在 Linux 中共有 7 种类型的文件，分为 3 大类：

1. 普通文件，包括文本文件和二进制文件
2. 目录文件（文件夹文件）
3. 特殊文件
    - :doc:`链接文件 <00_link>`
    - 字符设备文件
    - 套接字（Socket）文件，用于网络通讯，一般由应用程序创建
    - 命名管道文件
    - 块文件


查看文件类型
************************************

ls 命令
====================================

使用 :ref:`ls -l <cmd_ls>` 命令查看文件时，第一位的符号说明：

======    ======
符号       意义
======    ======
``–``      普通文件
``d``      目录文件
``l``      链接文件
``c``      字符设备文件
``s``      套接字（Socket）文件
``p``      命名管道文件
``b``      块文件
======    ======

.. highlight:: none

::

    [Linux]$ ls -l
    total 8
    drwxrwxr-x 2 glenn glenn 4096 Aug 30 21:41 images
    - rw-rw-r-- 1 glenn glenn  141 Jul 10 09:53 index.html


file 命令
====================================

可以用  :ref:`file <cmd_file>` 命令，查看某个文件的类型信息：

::

    # 查看普通文件
    [Linux]$ file powertop.html
    powertop.html: HTML document, ASCII text, with very long lines

    # 查看目录文件
    [Linux]$ ffile Pictures/
    Pictures/: directory

    # 查看链接文件
    [Linux]$ file log
    log: symbolic link to /run/systemd/journal/dev-log

    # 查看字符设备文件
    [Linux]$ file vcsu
    vcsu: character special (7/64)

    # 查看查看块文件
    [Linux]$ file sda1
    sda1: block special (8/1)

    # 查看套接字文件
    [Linux]$ file system_bus_socket
    system_bus_socket: socket

    # 查看命名管道文件
    [Linux]$ file pipe-test
    pipe-test: fifo (named pipe)


stat 命令
====================================

也可以用  :ref:`stat <cmd_stat>` 命令，查看某个文件的类型信息：

::

    # 查看普通文件
    [Linux]$ stat 2daygeek_access.log
      File: 2daygeek_access.log
      Size: 14406929    Blocks: 28144      IO Block: 4096   regular file
    Device: 10301h/66305d   Inode: 1727555     Links: 1
    Access: (0644/-rw-r--r--)  Uid: ( 1000/ daygeek)   Gid: ( 1000/ daygeek)
    Access: 2019-01-03 14:05:26.430328867 +0530
    Modify: 2019-01-03 14:05:26.460328868 +0530
    Change: 2019-01-03 14:05:26.460328868 +0530
     Birth: -
     
    # 查看目录文件
    [Linux]$ stat Pictures/
      File: Pictures/
      Size: 4096        Blocks: 8          IO Block: 4096   directory
    Device: 10301h/66305d   Inode: 1703982     Links: 3
    Access: (0755/drwxr-xr-x)  Uid: ( 1000/ daygeek)   Gid: ( 1000/ daygeek)
    Access: 2018-11-24 03:22:11.090000828 +0530
    Modify: 2019-01-05 18:27:01.546958817 +0530
    Change: 2019-01-05 18:27:01.546958817 +0530
     Birth: -

    # 查看链接文件
    [Linux]$ stat /dev/log
      File: /dev/log -> /run/systemd/journal/dev-log
      Size: 28          Blocks: 0          IO Block: 4096   symbolic link
    Device: 6h/6d   Inode: 278         Links: 1
    Access: (0777/lrwxrwxrwx)  Uid: (    0/    root)   Gid: (    0/    root)
    Access: 2019-01-05 16:36:31.033333447 +0530
    Modify: 2019-01-05 16:36:30.766666768 +0530
    Change: 2019-01-05 16:36:30.766666768 +0530
     Birth: -
        
    # 查看字符设备文件
    [Linux]$ stat /dev/vcsu
      File: /dev/vcsu
      Size: 0           Blocks: 0          IO Block: 4096   character special file
    Device: 6h/6d   Inode: 16          Links: 1     Device type: 7,40
    Access: (0660/crw-rw----)  Uid: (    0/    root)   Gid: (    5/     tty)
    Access: 2019-01-05 16:36:31.056666781 +0530
    Modify: 2019-01-05 16:36:31.056666781 +0530
    Change: 2019-01-05 16:36:31.056666781 +0530
     Birth: -

    # 查看查看块文件
    [Linux]$ stat /dev/sda1
      File: /dev/sda1
      Size: 0           Blocks: 0          IO Block: 4096   block special file
    Device: 6h/6d   Inode: 250         Links: 1     Device type: 8,1
    Access: (0660/brw-rw----)  Uid: (    0/    root)   Gid: (  994/    disk)
    Access: 2019-01-05 16:36:31.596666806 +0530
    Modify: 2019-01-05 16:36:31.596666806 +0530
    Change: 2019-01-05 16:36:31.596666806 +0530
     Birth: -

    # 查看套接字文件
    [Linux]$ stat /var/run/dbus/system_bus_socket
      File: /var/run/dbus/system_bus_socket
      Size: 0           Blocks: 0          IO Block: 4096   socket
    Device: 15h/21d Inode: 576         Links: 1
    Access: (0666/srw-rw-rw-)  Uid: (    0/    root)   Gid: (    0/    root)
    Access: 2019-01-05 16:36:31.823333482 +0530
    Modify: 2019-01-05 16:36:31.810000149 +0530
    Change: 2019-01-05 16:36:31.810000149 +0530
     Birth: -

    # 查看命名管道文件
    [Linux]$ stat pipe-test 
      File: pipe-test
      Size: 0           Blocks: 0          IO Block: 4096   fifo
    Device: 10301h/66305d   Inode: 1705583     Links: 1
    Access: (0644/prw-r--r--)  Uid: ( 1000/ daygeek)   Gid: ( 1000/ daygeek)
    Access: 2019-01-06 02:00:03.040394731 +0530
    Modify: 2019-01-06 02:00:03.040394731 +0530
    Change: 2019-01-06 02:00:03.040394731 +0530
     Birth: -
