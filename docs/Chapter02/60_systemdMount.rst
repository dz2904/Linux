Systemd 开机自动挂载硬盘
####################################

Systemd 的 Mount Unit 单元可以实现挂载硬盘，并且可以设定为开机自启动。相较于使用 ``/etc/fstab`` 文件来实现开机自动挂载硬盘，采用 Systemd 的方式更灵活，也不会因为配置错误导致无法开机，所以比较适合折腾。


新建挂载文件
************************************

在 ``/usr/lib/systemd/system/`` 目录下，新建文件并以 ``.mount`` 结尾。

.. highlight:: none

::

    [linux]# cat /usr/lib/systemd/system/home-disk.mount

    [Unit]
    Description=mount my videos disk
    After=home.mount
    
    [Mount]
    #What=UUID=ea8d7bf8-384d-412b-b9eb-b92f702fc360
    What=/dev/sdb2
    Where=/home/user/Videos
    Type=ext4
    Options=defaults
    
    [Install]
    WantedBy=multi-user.target


在加入开机启动之前，首先应该测试一下挂载文件的正确性：

::

    [Linux]$ systemctl start home-disk.mount
 

添加开机启动
************************************

::

    [Linux]# systemctl enable home-disk.mount
