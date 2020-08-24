history 命令行历史列表
####################################

history 命令保存从该终端会话运行的所有其它命令的列表，然后允许你重放或者重用这些命令，而不用重新输入它们。

可以在用户的家目录下找到 ``.bash_history`` 命令列表记录文件。


命令格式：
************************************

.. highlight:: none

::

    history [option]


常用选项：
************************************

::

    -c
    清空当前历史命令

    n
    显示最近的 n 条历史命令


.. hint ::

    <Ctrl+R> 快捷键可以对命令历史进行搜索，对于想要重复执行某个命令的时候非常有用。当找到命令后，再按回车键直接执行该命令，如果想对找到的命令进行调整后再执行，则可以按一下左或右方向键。

    # 不输入命令，直接按 <Ctrl+R>  进入搜索模式，输入单词自动匹配最近的历史记录
    (reverse-i-search)`red‘: cat /etc/redhat-release
    # cat /etc/redhat-release
    Fedora release 9 (Sulphur)


使用实例：
************************************

::

    [Linux]$ history
    1  service network restart
    2  exit
    3  id
    4  cat /etc/redhat-release
    5  history
    [Linux]$ !4
    cat /etc/redhat-release
    Fedora release 9 (Sulphur)

