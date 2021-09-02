未找到命令的解决办法
####################################

由于 Linux 的发行版多种多样，有时在终端中输入部分常用命令会找不到，如下：

.. highlight:: none

::

    [Linux]$ fdisk
    bash: Command 'fdisk' not found


**原因分析：**

首先应该使用 :ref:`whereis 命令 <cmd_whereis>` 查看系统中是否包含该命令，如果没有找到命令则需要安装。

由于部分命令运行时需要 root 权限，在某些发行版中会将其放入 ``/sbin`` 目录中，而在命令搜索路径 ``$PATH`` 中又不包含 ``/sbin`` 目录，所以会导致未找到命令。

::

    # 查找 fdisk 命令的路径
    [Linux]$ whereis fdisk
    fdisk: /sbin/fdisk /usr/share/man/man8/fdisk.8.gz

    # 查看命令搜索路径
    [Linux]$ echo $PATH
    /usr/local/bin:/usr/bin:/bin:/usr/local/games:/usr/games


解决方法：
************************************

**方法一：** 使用 ``su -`` 命令直接切换到 root 用户及其环境变量中，注意 su 命令后要带有中划线。

**方法二：** 使用 <Ctrl+Alt+F1-7> 切换虚拟终端，以 root 用户登陆。

**方法三：** 将没有的命令路径添加到 ``$PATH`` 变量中（如果想永久生效请将 ``export PATH=/sbin`` 添加到配置文件中）。

::

    # 临时修改变量
    [Linux]$ PATH=$PATH:/sbin

