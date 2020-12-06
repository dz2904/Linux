.. _cmd_head:

head 查看文件的开头内容
####################################

head 命令用于查看文件开头部分的内容，默认为前 10 行的内容。


命令格式：
************************************

.. highlight:: none

::

    head [OPTION]... [FILE]...

    
常用选项：
************************************

::

    -n, --lines=[-]NUM
        指定查看的行数

    -q, --quiet, --silent
        不显示文件名


使用实例：
************************************

::

    # 默认输出
    [Linux]$ head /etc/samba/smb.conf
    [global]
       map to guest = Bad User
       log file = /var/log/samba/%m
       log level = 1

    [guest]
       path = /mnt/samba/
       read only = no
       guest ok = yes
       guest only = yes

    # 指定显示行数
    [Linux]$ head -n 3 /etc/samba/smb.conf
    [global]
       map to guest = Bad User
       log file = /var/log/samba/%m
