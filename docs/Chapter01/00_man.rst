man 查看命令手册
####################################

Linux 提供了丰富的帮助手册，通过 man 命令可以查看 Linux 中的命令帮助、配置文件帮助和编程帮助等信息。 


命令格式：
************************************

.. highlight:: none

::

    man [man options] [[section] page ...] ...
    man -k [apropos options] regexp ...
    man -K [man options] [section] term ...
    man -f [whatis options] page ...
    man -l [man options] file ...
    man -w|-W [man options] page ...


常用选项：
************************************

::

    -f：等价于 whatis 指令，显示命令的简短描述信息
    -k：等价于 apropos 指令，查找包含指定字符的命令描述信息


使用实例：
************************************

::

    # 查看man的帮助手册
    [shiyanlou:/home/]$ man man
    MAN(1)                        Manual pager utils                        MAN(1)

    NAME
           man - an interface to the system reference manuals

    SYNOPSIS
           man [man options] [[section] page ...] ...
           man -k [apropos options] regexp ...
           man -K [man options] [section] term ...
           man -f [whatis options] page ...
           man -l [man options] file ...
           man -w|-W [man options] page ...

    DESCRIPTION
           man  is  the system's manual pager.  Each page argument given to man is
           normally the name of a program, utility or function.  The  manual  page
           associated with each of these arguments is then found and displayed.  A
           section, if provided, will direct man to look only in that  section  of
           the  manual.   The  default action is to search in all of the available
           sections following a pre-defined order (see DEFAULTS), and to show only
           the first page found, even if page exists in several sections.
           
           ......


Linux 手册格式
************************************

Linux 帮助手册是一个庞大的参考书，为了更好的组织信息，Linux 将整个手册页分成了8章（编号1～8）。各个发行版之间实际名称可能会有所不同，但是，所有的 Linux 手册都倾向于遵循相同的组织原则。

=====     =====
章节          说明
=====     =====
1                 标准用户命令（包含大量的命令手册）
2                系统调用（在程序中使用，用来请求内核执行指令）
3                 库调用
4                 特殊文件（包含物理设备和设备的驱动信息）
5                 文件格式（包含配置文件）
6                 游戏
7                 杂项（各种混杂信息）
8                 管理命令（系统管理员使用的特殊命令）
=====     =====

.. note::

    在查看帮助手册时，在第一行的左上角和右上角都会显示命令所在的章节，如：MAN(1) 

在每章下包含各自的命令帮助手册。每个命令的帮助手册，也会按照标准格式组织，将每个页面分成几个部分，每个部分都拥有独立的标题。

=============     =============
标题                                 含义
=============     =============
NAME                              命令名称及功能
SYNOPSIS                      命令语法，包括可用的选项
DESCRIPTION              命令功能的详细说明
OPTIONS                        每个选项的详细说明
EXAMPLES                    用法示例
ENVIRONMENT          命令使用的环境变量
FILES                                命令相关的配置文件       
AUTHOR                         程序的作者
BUGS                               错误、缺点、警告 
COPYRIGHT                  版权信息
SEE ALSO                      参照、相关信息
=============     =============
    
.. note::

    在通过 man 查看帮助手册时，命令语法遵循7条规则。

    1. 方括号中的项是可选的
    2. 不在方括号中的项是必选项
    3. 黑体字必须按原样准确输入
    4. 斜体字必须用适当的值替代（如果没有斜体字会用下划线代替）
    5. 后面跟省略号的参数可以有多个
    6. 单独的选项和参数组合在一起，那么选项和参数必须同时使用。如 ``[ -P pager]``
    7. 由竖线字符分开的两个或多个选项，可以任选其一。如 ``[ file | arg1 arg2]``

在 man 后边直接跟命令名称，Linux 会从第1章开始向后查找，然后输出找到的第一个匹配。如果想查看后边章的帮助手册就需要指定章节号。

::

    [shiyanlou:/home/]$ man kill
    KILL(1)                          User Commands                         KILL(1)

    NAME
           kill - send a signal to a process

    SYNOPSIS
           kill [options] <pid> [...]

    DESCRIPTION
           The  default  signal  for kill is TERM.  Use -l or -L to list available
           signals.  Particularly useful signals include  HUP,  INT,  KILL,  STOP,
           CONT,  and  0.   Alternate  signals may be specified in three ways: -9,
           -SIGKILL or -KILL.  Negative PID values may be  used  to  choose  whole
           process  groups; see the PGID column in ps command output.  A PID of -1
           is special; it indicates all processes except the kill  process  itself
           and init.


    [shiyanlou:/home/]$ man 2 kill
    KILL(2)                    Linux Programmer's Manual                   KILL(2)

    NAME
           kill - send signal to a process

    SYNOPSIS
           #include <sys/types.h>
           #include <signal.h>

           int kill(pid_t pid, int sig);

       Feature Test Macro Requirements for glibc (see feature_test_macros(7)):

           kill(): _POSIX_C_SOURCE

可以使用 whatis 命令确定所查询的命令包含在几个章节中，括号中的数字即为章节号。

::

    [shiyanlou:/home/]$ whatis kill
    kill (1)             - send a signal to a process
    kill (2)             - send signal to a process
