.. _cmd_kill:

kill 终止进程
####################################

kill 命令大多时候用于终止（删除）执行中的进程。

kill 默认发送的信息为 SIGTERM(15)，可终止指定程序。若仍无法终止该程序，可使用 SIGKILL(9) 信息尝试强制终止程序。

程序的进程号可使用 :ref:`ps 命令 <cmd_ps>` 或 :ref:`jobs -l 命令 <cmd_jobs>` 查看，还可以使用 :ref:`top 命令 <cmd_top>` 。


命令格式：
************************************

.. highlight:: none

::

    kill -<signal> <pid> [...]
    kill -s <signal> <pid> [...]
    kill --signal <signal> <pid> [...]


常用选项：
************************************

::

    -l, --list [signal]
        若不加 [signal] 选项，则列出全部的信号编号和名称
        加 [signal] 选项，则在信号编号和信号名称之间相互转换

    -L
        列出全部的信号编号和名称


经常使用的信号：

===============   ===============   ==========
Signal Name        Single Value      Effect
===============   ===============   ==========
SIGINT             2                 中断信号（同 <Ctrl+C> ）
SIGKILL            9                 强制终止进程
SIGTERM            15                发送终止信号（不适用卡死的进程）
SIGCONT            18                继续进程（类似 bg 命令）
SIGSTOP            19                暂停进程（同 <Ctrl+Z> ）
===============   ===============   ==========


使用实例：
************************************

::

    # 列出所有信号编号和名称
    [Linux]$ kill -l
     1) SIGHUP	    2) SIGINT       3) SIGQUIT       4) SIGILL       5) SIGTRAP
     6) SIGABRT	    7) SIGBUS       8) SIGFPE        9) SIGKILL     10) SIGUSR1
    11) SIGSEGV	    12) SIGUSR2     13) SIGPIPE     14) SIGALRM     15) SIGTERM
    16) SIGSTKFLT   17) SIGCHLD	    18) SIGCONT     19) SIGSTOP     20) SIGTSTP
    21) SIGTTIN     22) SIGTTOU	    23) SIGURG      24) SIGXCPU     25) SIGXFSZ
    26) SIGVTALRM   27) SIGPROF     28) SIGWINCH    29) SIGIO       30) SIGPWR
    31) SIGSYS      34) SIGRTMIN    35) SIGRTMIN+1  36) SIGRTMIN+2  37) SIGRTMIN+3
    38) SIGRTMIN+4  39) SIGRTMIN+5  40) SIGRTMIN+6  41) SIGRTMIN+7  42) SIGRTMIN+8
    43) SIGRTMIN+9  44) SIGRTMIN+10 45) SIGRTMIN+11	46) SIGRTMIN+12	47) SIGRTMIN+13
    48) SIGRTMIN+14	49) SIGRTMIN+15	50) SIGRTMAX-14	51) SIGRTMAX-13	52) SIGRTMAX-12
    53) SIGRTMAX-11	54) SIGRTMAX-10	55) SIGRTMAX-9  56) SIGRTMAX-8  57) SIGRTMAX-7
    58) SIGRTMAX-6  59) SIGRTMAX-5  60) SIGRTMAX-4  61) SIGRTMAX-3  62) SIGRTMAX-2
    63) SIGRTMAX-1  64) SIGRTMAX	


    # 首先查看进程号，然后终止进程
    [Linux]$ ps -ef | grep gvim
    UID          PID    PPID  C STIME TTY          TIME CMD
    glenn       4071    1246  1 20:47 ?        00:00:35 gvim new.txt
    [Linux]$ kill -9 4071

    # 等同于 -9
    [Linux]$ kill -KILL 4071
