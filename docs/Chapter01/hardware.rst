查看内核和硬件信息
######################################

1. 查看 Linux 内核版本：

.. highlight:: none

::

    uname -a

2. 查看 CPU 信息：

::

    more /proc/cpuinfo

    # 查看 CPU 型号
    grep "model name" /proc/cpuinfo

    # 查看 CPU 位数（32 or 64）：
    echo $HOSTTYPE

3. 查看内存信息：

::

    more /proc/meminfo

    # 查看内存大小：
    grep MemTotal /proc/meminfo

4. 查看硬盘和分区：

::

    df -h

    # 查看目录的大小
    du /etc/ -sh

5. 查看主机名：

::

    hostname

6. 查看默认语言：

::

    echo $LANG $LANGUAGE
