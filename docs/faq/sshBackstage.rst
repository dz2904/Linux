SSH 断开后 进程仍在后台运行
#######################################

对于 Linux 运维，经常会使用 ssh 登录到服务器。如果任务需要很长时间或不间断运行，在关闭终端窗口或网络不稳定的情况下，任务就会中断。
当然这只对于普通程序，不包括如 mysqld、httpd 这样的守护进程。

**原因分析：**

.. highlight:: none

::

    [root@DigMouse ~]# ping www.baidu.com > /dev/null &
    [1] 21945
    [root@DigMouse ~]# pstree -H 21945
    systemd─┬─AliYunDun───17*[{AliYunDun}]
            ├─AliYunDunUpdate───3*[{AliYunDunUpdate}]
            ├─acpid
            ├─2*[agetty]
            ├─aliyun-service───5*[{aliyun-service}]
            ├─atd
            ├─cron
            ├─dbus-daemon
            ├─nscd─┬─{nscd) S 1 434 43
            │      └─9*[{nscd}]
            ├─ntpd───ntpd
            ├─python3
            ├─rpcbind
            ├─rsyslogd─┬─{in:imklog}
            │          ├─{in:imuxsock}
            │          └─{rs:main Q:Reg}
            ├─sshd───sshd───bash─┬─ping
            │                    └─pstree
            ├─systemd-journal
            ├─systemd-logind
            ├─systemd-network
            ├─systemd-udevd
            ├─vsftpd
            └─wrapper─┬─java───13*[{java}]
                      └─{wrapper}


当用户注销（logout）或者网络断开时，终端会收到 HUP（hangup）信号从而关闭其所有子进程。
从上面的例子可以看出当前所处的 bash 是 sshd 的子进程，当 ssh 断开连接时，HUP 信号会影响到它下面的所有子进程，包括 ping 进程。

**解决思路：**

1. 让进程运行在新的 session（会话）里即不属于此终端的子进程。
2. 让进程忽略 HUP 信号

解决方法：
********************

1. nohup 命令
========================

功能：不挂断地运行命令，忽略HUP信号。

语法：nohup command &

实例：

::

    [root@DigMouse ~]# nohup ping www.baidu.com &
    [1] 22050
    [root@DigMouse ~]# nohup: ignoring input and redirecting stderr to stdout
    [root@DigMouse ~]# ls
    nohup.out
    [root@DigMouse ~]# cat nohup.out
    PING www.wshifen.com (103.235.46.39) 56(84) bytes of data.
    64 bytes from 122.225.57.246: icmp_seq=1 ttl=56 time=48.6 ms
    64 bytes from 122.225.57.246: icmp_seq=2 ttl=56 time=47.8 ms
    64 bytes from 122.225.57.246: icmp_seq=3 ttl=56 time=49.9 ms
    64 bytes from 122.225.57.246: icmp_seq=4 ttl=56 time=49.5 ms


    # 从另一个终端执行以下命令
    [root@DigMouse ~]# ps -ef | grep ping
    root     22050 21736  0 21:01 pts/0    00:00:00 ping www.baidu.com
    root     22070 21736  0 21:04 pts/0    00:00:00 grep ping

关闭此终端，打开另一个终端使用 ps 命令，查看到后台有 ping 进程在运行。
终端输出将附加到当前目录的 nohup.out 文件中。如果当前目录的 nohup.out 文件不可写，输出将重定向到 $HOME/nohup.out 文件中。

2. setsid命令
=========================

功能：在新的会话中运行程序

语法：setsid command

实例：

::

    [root@DigMouse ~]# setsid ping www.baidu.com > /dev/null
    [root@DigMouse ~]# ps -ef | grep ping
    UID        PID  PPID  C STIME TTY          TIME CMD
    root     22146     1  0 21:14 ?        00:00:00 ping www.baidu.com
    root     22152 21736  0 21:15 pts/0    00:00:00 grep ping
    [root@DigMouse ~]#

从上例可以看出 ping 进程的 PID 是 22146，进程的父 ID(PPID) 是 init 而不是当前终端的进程 ID，可与 nohup 比较。

3. 将"&"也放入“()”内执行命令
===============================

将一个或多个命名包含在“()”中就能让这些命令在子 shell 中运行

::

    [root@DigMouse ~]# (ping www.baidu.com > /dev/null &)
    [root@DigMouse ~]# ps -ef | grep ping
    root     22202     1  0 21:22 pts/0    00:00:00 ping www.baidu.com
    root     22204 21736  0 21:22 pts/0    00:00:00 grep ping
    [root@DigMouse ~]#

进程的父 ID 是 init 而不是当前终端的进程 ID，因而关闭终端无任何影响。

4. disown 命令
================================

用 disown -h jobspec 来使某个作业忽略 HUP 信号。

用 disown -ah 来使所有的作业都忽略 HUP 信号。

用 disown -rh 来使正在运行的作业忽略 HUP 信号。

当使用过 disown 之后，会将把目标作业从作业列表中移除，我们将不能再使用 jobs 来查看它，但是依然能够用 ps -ef 查找到它。

但是还有一个问题，这种方法的操作对象是作业，如果我们在运行命令时在结尾加了"&"来使它成为一个作业并在后台运行，那么就万事大吉了，我们可以通过 jobs 命令来得到所有作业的列表。但是如果并没有把当前命令作为作业来运行，如何才能得到它的作业号呢？答案就是用 CTRL-z（按住Ctrl键的同时按住z键）了！

CTRL-z 的用途就是将当前进程挂起（Suspend），然后我们就可以用 jobs 命令来查询它的作业号，再用 bg jobspec 来将它放入后台并继续运行。
需要注意的是，如果挂起会影响当前进程的运行结果，请慎用此方法。

::

    # 将程序放入后台，暂停执行（挂起进程）
    [root@DigMouse ~]# ping www.baidu.com > /dev/null
    ^Z
    [1]+  Stopped                 ping www.baidu.com > /dev/null
    [root@DigMouse ~]# jobs
    [1]+  Stopped                 ping www.baidu.com > /dev/null

    # 执行后台的作业
    [root@DigMouse ~]# bg %1
    [1]+ ping www.baidu.com > /dev/null &
    [root@DigMouse ~]# jobs
    [1]+  Running                 ping www.baidu.com > /dev/null &

    # 此时 jobs 还是能看到 ping 后台任务的。
    [root@DigMouse ~]# ps -ef | grep ping
    root     22256 21736  0 21:32 pts/0    00:00:00 ping www.baidu.com
    root     22273 21736  0 21:35 pts/0    00:00:00 grep ping

    [root@DigMouse ~]# disown -h %1

    # 需要断开链接后，重新登录
    [root@DigMouse ~]# ps -ef | grep ping
    root     22256     1  0 21:32 ?        00:00:00 ping www.baidu.com
    root     22390 22381  0 21:50 pts/1    00:00:00 grep ping


5. screen 命令（未做测试）
==============================

此命令非常强大。如果非常多的命令都需要忽略 HUP 命令，screen 可以解决这一问题。screen 提供了 ANSI/VT100 的终端模拟器，使它能够在一个真实终端下运行多个全屏的伪终端。

* 用 screen -dmS session name 来建立一个处于断开模式下的会话（并指定其会话名）。
* 用 screen -list 来列出所有会话。
* 用 screen -r session name 来重新连接指定会话。
* 用快捷键 CTRL-a d 来暂时断开当前会话。

此时 bash 是 screen 的子进程，而 screen 是 init（PID为1）的子进程。那么当 ssh 断开连接时，HUP 信号自然不会影响到 screen 下面的子进程了。


脚本：disown,&和nohup的区别
*****************************

总结来说：

&： 将进程置于后台，使Shell不用等待它的结束而继续接受用户输入(stdin)。

disown: 将进程从jobs列表中移除, 但依然与Shell有连接

nohup: 将进程与父Shell完全脱离，且子进程不会接受NOHUP信号，并不能用fg或者jobs命令找到它。
