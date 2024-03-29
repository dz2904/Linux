.. _cmd_jobs:

jobs 查看后台命令
####################################

用来查看当前终端有多少在后台运行的命令。

.. hint::

    可以使用 ``bg %1`` 命令让程序在后台继续执行，也可以使用 ``fg %1`` 命令把后台命令恢复到前台执行。


命令格式：
************************************

.. highlight:: none

::

    jobs [-lnprs] [jobspec ...]

    
常用选项：
************************************

::

    -l
        在正常信息基础上，列出进程的 PID 

    -n
        只列出上次发出通知后改变了状态的进程

    -p
        只列出进程的 PID

    -r
        只列出运行中的进程

    -s
        只列出已停止的进程


使用实例：
************************************

::

    [Linux]$ jobs
    [1]+  Stopped                 vim

    [Linux]$ jobs -l
    [1]+   494 Stopped                 vim

