一切皆文件
####################################

众所周知，在 Linux 中一切皆为文件，包括硬盘、显卡、打印机等硬件设备和程序、进程等软件。当然系统中大部分的文件都是普通文件和目录文件。

在 Linux 中共有 7 种类型的文件，分为 3 大类：

1. 普通文件
2. 目录文件
3. 特殊文件
    - 链接文件
    - 字符设备文件
    - 套接字（Socket）文件
    - 命名管道文件
    - 块文件


查看文件类型
************************************

ls 命令
====================================

使用 :ref:`ls -l <cmd_ls>` 命令查看文件时，第一位的符号说明：

======    ======
符号　　	　意义
======    ======
``–``    普通文件
``d``    目录文件
``l``    链接文件
``c``    字符设备文件
``s``    套接字（Socket）文件
``p``    命名管道文件
``b``    块文件
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
