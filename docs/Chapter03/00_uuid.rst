硬盘分区的 UUID
####################################

UUID 是指通用唯一识别码（Universally Unique Identifier），用于帮助 Linux 系统识别一个磁盘分区而不是块设备文件。UUID 在系统中使用一个 128 位（比特）的数字来标识。

UUID 最初被用在阿波罗网络计算机系统（NCS）中，之后 UUID 被开放软件基金会（OSF）标准化，成为分布式计算环境（DCE）的一部分。自 Linux 内核 2.15.1 起，libuuid 就是 util-linux-ng 包中的一部分，它被默认安装在 Linux 系统中，UUID 由该库生成，可以认为在一个系统中 UUID 是唯一的，并且在所有系统中也是唯一的。


UUID 以 32 个十六进制的数字表示，被连字符分割为 5 组（格式为 8-4-4-4-12）。现在大多数的 Linux 系统都使用 UUID 挂载分区，这一点可以在 /etc/fstab 文件中验证：

.. highlight:: none

::

    # cat /etc/fstab
    # /etc/fstab: static file system information.
    #
    # Use 'blkid' to print the universally unique identifier for a device; this may
    # be used with UUID= as a more robust way to name devices that works even if
    # disks are added and removed. See fstab(5).
    #
    #
    UUID=69d9dd18-36be-4631-9ebb-78f05fe3217f / ext4 defaults,noatime 0 1
    UUID=a2092b92-af29-4760-8e68-7a201922573b swap swap defaults,noatime 0 2


查看 UUID
************************************

在查看硬盘的 UUID 之前，最好先 :ref:`mount <cmd_mount>` 挂载硬盘。


查看 by-uuid 文件夹
====================================

Linux 中 /dev/disk/by-uuid/ 目录包含了 UUID 与实际的块设备文件链接。

::

    [Linux]$ ls -lh /dev/disk/by-uuid/
    total 0
    lrwxrwxrwx 1 root root 10 Jan 29 08:34 ca307aa4-0866-49b1-8184-004025789e63 -> ../../sdc3
    lrwxrwxrwx 1 root root 10 Jan 29 08:34 d17e3c31-e2c9-4f11-809c-94a549bc43b7 -> ../../sdc1
    lrwxrwxrwx 1 root root 10 Jan 29 08:34 d92fa769-e00f-4fd7-b6ed-ecf7224af7fa -> ../../sda1


lsblk 命令
====================================

使用 :ref:`lsblk <cmd_lsblk>` 命令列出所有有关可用或指定块设备的信息。lsblk 命令读取 sysfs 文件系统和 udev 数据库以收集信息。

如果 udev 数据库不可用或者编译的 lsblk 不支持 udev，它会试图从块设备中读取卷标、UUID 和文件系统类型。这种情况下，必须以 root 身份运行。该命令默认会以类似于树的格式打印出所有的块设备（RAM 盘除外）。

::

    [Linux]# lsblk -o name,mountpoint,size,uuid
    NAME   MOUNTPOINT  SIZE UUID
    sda                 30G 
    └─sda1 /            20G d92fa769-e00f-4fd7-b6ed-ecf7224af7fa
    sdb                 10G 
    sdc                 10G 
    ├─sdc1               1G d17e3c31-e2c9-4f11-809c-94a549bc43b7
    ├─sdc3               1G ca307aa4-0866-49b1-8184-004025789e63
    ├─sdc4               1K 
    └─sdc5               1G 
    sdd                 10G 
    sde                 10G 
    sr0               1024M 


blkid 命令
====================================

:ref:`blkid <cmd_blkid>` 是定位或打印块设备属性的命令行实用工具。它利用 libblkid 库在 Linux 系统中获得到磁盘分区的 UUID。

::

    [Linux]$ blkid
    /dev/sda1: UUID="d92fa769-e00f-4fd7-b6ed-ecf7224af7fa" TYPE="ext4" PARTUUID="eab59449-01"
    /dev/sdc1: UUID="d17e3c31-e2c9-4f11-809c-94a549bc43b7" TYPE="ext2" PARTUUID="8cc8f9e5-01"
    /dev/sdc3: UUID="ca307aa4-0866-49b1-8184-004025789e63" TYPE="ext4" PARTUUID="8cc8f9e5-03"
    /dev/sdc5: PARTUUID="8cc8f9e5-05"
