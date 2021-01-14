关闭终端响铃
####################################

在使用终端自动补全功能时会发出讨厌的嘟嘟声，可通过编辑配置文件 ``/etc/inputrc`` 关闭终端响铃：

.. highlight:: none

::

    # 取消该行的注释，或添加该行
    [Linux]# vim /etc/inputrc

    set bell-style none


关闭 vi 响铃
************************************

在 vi 的配置文件 ``/etc/virc`` 中，添加一行：

::

    set vb t_vb=


注意：vim 的配置文件是 ``/etc/vimrc`` 。