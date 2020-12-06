.. _cmd_type:

type 查看命令类型
####################################

type 命令用于查看命令的类型，它是 shell 的内部命令，命令类型可以分为以下几种：

1. builtin 内建命令。Shell 内建命令，例如：cd 命令。
2. keyword 关键字，Shell 保留字。
3. function 函数，Shell 函数是小规模的 Shell 脚本，它们混合到环境变量中。
4. alias 命令别名。
5. file 文件。磁盘文件、外部命令。
6. unfound 没有找到。


命令格式：
************************************

.. highlight:: none

::

    type [-aftpP] name [name ...]


常用选项：
************************************

::

    -a
        显示所有匹配的类型

    -p
        如果给出的指令为外部指令，则显示其绝对路径


使用实例：
************************************

::

    # 查看 ls 的命令类型
    [Linux]$ type ls
    ls is aliased to `ls --color=tty'

    # 查看所有匹配 ls 的命令类型
    [Linux]$ type -a ls
    ls is aliased to `ls --color=tty'
    ls is /bin/ls

    # 各种类型的输出
    [Linux]$ type cd
    cd is a shell builtin

    [Linux]$ type date
    date is /bin/date

    [Linux]$ type mysql
    mysql is /usr/bin/mysql

    [Linux]$ type nginx
    -bash: type: nginx: not found

    [Linux]$ type if
    if is a reserved word
