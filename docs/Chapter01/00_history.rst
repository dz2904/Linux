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

    <Ctrl+R> 快捷键可以以递增方式对命令历史进行搜索，对于想要重复执行某个命令的时候非常有用。当找到要查找的命令时，按 <Enter> 键执行此命令，按 <Ctrl+J> 键将搜索到的命令从历史记录列表中复制到当前命令行来进一步编辑，再次按 <Ctrl+R> 键将查找下一个匹配项（即向前搜索历史记录）。若要退出搜索，可以按 <Ctrl+G> 或者 <Ctrl+C> 即可。

    ::

    # 不输入命令，直接按 <Ctrl+R>  进入搜索模式，输入单词自动匹配最近的历史记录
    # 提示符会变为 (reverse-i-search)`':
    (reverse-i-search)`red‘: cat /etc/redhat-release


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

Shell 中还有几个好用的历史列表命令：

==========   ==============
字符           说明
==========   ==============
!!             执行上一条命令（等同于 <Up> + <Enter> 键）
!number        执行历史列表中第 number 行的命令
!string        执行最近的以 string 字符串开头的命令
!?string       执行最近的包含这个字符串的命令
!*             使用上一条命令的选项和参数
!$             使用上一条命令的最后一个参数
==========   ==============


.. hint ::

    应该谨慎地使用 ``!string`` 和 ``!?string`` 格式，除非你完全确信历史列表条目的内容。


::

    [Linux]$ ls -l Music/
    total 0
    [Linux]$ !!
    ls -l Music/
    total 0
    [Linux]$ ls !*
    ls -l Music/
    total 0
    [Linux]$ ls !$
    ls Music/
