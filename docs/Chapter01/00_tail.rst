tail 查看文件的末尾内容
####################################

tail 命令用于查看文件的末尾内容，默认为文件末尾的 10 行。

命令格式：
************************************

.. highlight:: none

::

    tail [OPTION]... [FILE]...


常用选项：
************************************

::

    -f, --follow[={name|descriptor}]
        循环读取文件内容，常用于查看日志写入

    -n, --lines=[+]NUM
        指定查看的行数，如果有 + 号，则查看该行之后的所有内容


使用实例：
************************************

::

    [Linux]$ tail /etc/samba/smb.conf
    [global]
       map to guest = Bad User
       log file = /var/log/samba/%m
       log level = 1

    [guest]
       path = /mnt/samba/
       read only = no
       guest ok = yes
       guest only = yes


    [Linux]$ tail -n 3 /etc/samba/smb.conf
       read only = no
       guest ok = yes
       guest only = yes


    [Linux]$ tail -n +3 /etc/samba/smb.conf
       log file = /var/log/samba/%m
       log level = 1

    [guest]
       path = /mnt/vmware/samba/
       read only = no
       guest ok = yes
       guest only = yes

