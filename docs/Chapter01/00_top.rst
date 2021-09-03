.. _cmd_top:

top 查看系统状态
####################################

top 命令可以实时动态地查看系统的整体运行情况，是一个综合了多方信息监测系统性能和运行信息的实用工具。同时 top 命令也提供互动式界面，用热键可以管理。


命令格式：
************************************

.. highlight:: none

::

    top -hv|-bcEeHiOSs1 -d secs -n max -u|U user -p pids -o field -w [cols]


常用选项：
************************************

::

    -d
        指定更新时间（单位秒，如果有小数点则换算成 ms，默认值 5）

    -u
        指定用户名

    -p
        指定进程号

    -n
        指定更新的次数，完成后退出

    -S
        累积模式


交互式命令：
====================================

======   ======
命令      说明
======   ======
h         显示帮助
k         终止一个进程，首先输入进程号，然后输入发送的信号（15 或 9）
i         显示/隐藏闲置和僵死的进程
q         退出
r         重新设置一个进程的优先级别（数值越小优先级越高）
S         切换到累计模式
s         改变更新时间
c         显示/隐藏完整命令行
l         显示/隐藏平均负载和启动时间信息（即第一行）
t         显示/切换/隐藏进程和 CPU 状态信息（即第二和第三行）
m         显示/切换/隐藏内存信息（即第四和第五行）
n         设置显示进程的数量
N         以 PID 的大小进行排序
P         以 CPU 占用率进行排序
M         以内存占用率大小进行排序
T         根据时间/累计时间进行排序
w         将当前设置写入 ``~/.toprc`` 文件中
======   ======


使用实例：
************************************

::

    # 默认输出
    [Linux]$ top
    top - 18:02:42 up  8:32,  1 user,  load average: 0.57, 0.61, 0.95
    Tasks: 251 total,   1 running, 250 sleeping,   0 stopped,   0 zombie
    %Cpu(s):  3.7 us,  2.2 sy,  0.0 ni, 94.0 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st
    MiB Mem :  23665.3 total,  20068.8 free,   1731.2 used,   1865.3 buff/cache
    MiB Swap:    977.0 total,    977.0 free,      0.0 used.  21354.1 avail Mem 

        PID USER      PR  NI    VIRT    RES    SHR S  %CPU  %MEM     TIME+ COMMAND                   
       1300 glenn     20   0 2469984  31268  22272 S   6.7   0.1   3:24.92 pulseaudio                
       1547 glenn     20   0  317344  23052   6744 S   6.7   0.1   2:11.17 ibus-daemon               
       8982 glenn     20   0 1856612 138784  77872 S   6.7   0.6   0:55.73 rhythmbox                 
       9735 glenn     20   0   10228   3788   3168 R   6.7   0.0   0:00.01 top                       
          1 root      20   0  164068  10516   7784 S   0.0   0.0   0:04.12 systemd                   
          2 root      20   0       0      0      0 S   0.0   0.0   0:00.04 kthreadd                  
          3 root       0 -20       0      0      0 I   0.0   0.0   0:00.00 rcu_gp         


输出说明：

第一行，系统运行时间和平均负载：

::

    top - 18:02:42 up  8:32,  1 user,  load average: 0.57, 0.61, 0.95

- 当前时间
- 已运行时间
- 当前登录用户的数量
- 最近 5、10 和 15 分钟内的系统平均负载

第二行，进程任务：

::

    Tasks: 251 total,   1 running, 250 sleeping,   0 stopped,   0 zombie

- total 系统进程总数
- running 处于运行中的进程
- sleeping 睡眠的进程总数
- stoped 停止的进程总数
- zombie 僵尸进程总数

第三行，CPU 状态：

::

    %Cpu(s):  3.7 us,  2.2 sy,  0.0 ni, 94.0 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st


不同模式下所占 cpu 时间百分比：

- us（user）： 运行(未调整优先级) 用户进程的 CPU 时间
- sy（system）: 运行内核进程的 CPU 时间
- ni（niced）：运行已调整优先级用户进程的 CPU 时间
- wa（IO wait）: 用于等待 IO 完成的 CPU 时间
- hi：处理硬件中断的 CPU 时间
- si: 处理软件中断的 CPU CPU时间
- st：虚拟机模式中处理 hypervisor 的 CPU 时间

第四和第五行，内存使用：

::

    MiB Mem :  23665.3 total,  20068.8 free,   1731.2 used,   1865.3 buff/cache
    MiB Swap:    977.0 total,    977.0 free,      0.0 used.  21354.1 avail Mem 

- Mem：物理内存
- Swap：虚拟内存
- total：内存总量
- used：使用中的内存总量
- free：空闲内存总量
- buffers：缓存的内存量

各进程的状态：

::

    PID USER      PR  NI    VIRT    RES    SHR S  %CPU  %MEM     TIME+ COMMAND                   

- PID：进程 ID，进程的唯一标识符
- USER：进程所有者（用户名）
- PR：进程的调度优先级，'rt'表示进程运行在实时态
- NI：进程的 nice 值（优先级）。越小的值意味着越高的优先级，可以是负值
- VIRT：进程使用的虚拟内存总量（单位 kb）
- RES：使用中的未被换出的物理内存大小（单位 kb）
- SHR：使用的共享内存大小（单位 kb）
- S：进程的状态:
    - D 不可中断的睡眠状态
    - R 运行状态
    - S 睡眠状态
    - T 被跟踪或已停止状态
    - Z 僵尸状态
- %CPU：两次更新时间之间所使用的 CPU 百分比
- %MEM：进程使用的可用物理内存百分比
- TIME+：任务启动后到现在所使用的全部 CPU 时间，精确到百分之一秒
- COMMAND：运行进程所使用的命令


.. hint:: 切换排序列

    top 默认是以 CPU 的占用比排序。可以通过 ``SHIFT+>`` 键向右或 ``SHIFT+>`` 向左改变排序列。
