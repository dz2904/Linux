id 命令
##########################

id 命令用于显示用户的 ID，以及所属群组的实际与有效 ID（若两个ID相同，则仅显示实际ID）。若没有指定用户名称，则显示当前用户的 ID。

命令格式：
***********************

.. highlight:: none

::

    id [OPTION]... [USER]

常用选项：
***********************

::

    -a
      可以忽略，只是为了与其他版本兼容。

     -g, --group
      只打印有效的组 ID

    -G, --groups
      打印所有组 ID

    -u, --user
      显示用户 ID

    --version
      输出版本信息

使用实例：
***********************

::

    [root@localhost ~]# id
    uid=0(root) gid=0(root) groups=0(root)

    # 输出特定用户名 ftp 的 ID 信息
    [root@localhost ~]# id ftp
    uid=14(ftp) gid=50(ftp) groups=50(ftp)
