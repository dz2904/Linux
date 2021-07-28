Systemd 定时器
############################

Systemd 定时器与 cron 作业类似，但是 Systemd 定时器可以做到 cron 作业无法做到的一些事情。例如，Systemd 定时器可以在特定事件发生后的一段时间后触发一段脚本或者程序去执行，例如开机、启动、上个任务完成，甚至于定时器调用的上个服务单元的完成的时刻。

其实每个 Linux 系统在安装完成后，都会或多或少的运行多个定时器任务，可以使用 ``systemctl status *timer`` 命令来查看主机上的所有的定时器单元。

.. highlight:: none

::

    [linux]$ systemctl status *timer
    ● logrotate.timer - Daily rotation of log files
       Loaded: loaded (/lib/systemd/system/logrotate.timer; enabled; vendor preset: enabled)
       Active: active (waiting) since Sun 2021-07-25 10:47:45 CST; 3 days ago
      Trigger: Thu 2021-07-29 00:00:00 CST; 10h left
         Docs: man:logrotate(8)
               man:logrotate.conf(5)

    ● apt-daily.timer - Daily apt download activities
       Loaded: loaded (/lib/systemd/system/apt-daily.timer; enabled; vendor preset: enabled)
       Active: active (waiting) since Sun 2021-07-25 10:47:45 CST; 3 days ago
      Trigger: Wed 2021-07-28 18:56:21 CST; 5h 3min left

    ● apt-daily-upgrade.timer - Daily apt upgrade and clean activities
       Loaded: loaded (/lib/systemd/system/apt-daily-upgrade.timer; enabled; vendor preset: en
       Active: active (waiting) since Sun 2021-07-25 10:47:45 CST; 3 days ago
      Trigger: Thu 2021-07-29 06:59:12 CST; 17h left


每个定时器至少有六行相关信息：

- 第一行是定时器名字和定时器目的的简短介绍
- Loaded: 定时器的状态，是否已加载，定时器单元文件的完整路径以及预设信息。
- Active: 活动状态，包括该定时器激活的日期和时间。
- Trigger: 定时器下次被触发的日期和时间和距离触发的大概时间。
- Triggers: 被定时器触发的事件或服务名称。
- Docs: 相关文档的指引。
- 最后一行是计时器最近触发的服务实例的日志条目。


创建定时器
****************************

在创建定时器之前，必须要有一个 Systemd 启动单元。

在 ``/etc/systemd/system`` 目录下创建 myMonitor.timer 定时器单元文件，添加如下代码：

::

    # This timer unit is for testing
    # By David Both
    # Licensed under GPL V2
    #
    [Unit]
    Description=Logs some system statistics to the systemd journal
    Requires=myMonitor.service
    [Timer]
    Unit=myMonitor.service
    OnCalendar=*-*-* *:*:00
    [Install]
    WantedBy=timers.target

其中，Unit 是需要运行的单元（脚本或程序）。OnCalendar 是时间格式，*-*-* *:*:00，应该会每分钟触发一次定时器去执行 myMonitor.service 单元。


时间格式
****************************

Systemd 定时器使用 ``OnCalendar`` 的基础格式是 ``DOW YYYY-MM-DD HH:MM:SS`` 。DOW（星期几）是选填的，其他字段可以用一个星号（ ``*`` ）来匹配此位置的任意值。所有的日历时间格式会被转换成标准格式。如果时间没有指定，它会被设置为 ``00:00:00`` 。如果日期没有指定但是时间指定了，那么下次匹配的时间可能是今天或者明天，取决于当前的时间。月份和星期可以使用名称或数字。每个单元都可以使用逗号分隔的列表。单元范围可以在开始值和结束值之间用 ``..`` 指定。

指定日期有一些有趣的选项，波浪号（ ``~`` ）可以指定月份的最后一天或者最后一天之前的某几天。 ``/`` 可以用来指定星期几作为修饰符。

这里有几个在 OnCalendar 表达式中使用的典型时间格式例子。

==============================   ==============================   
日期事件格式                      描述
==============================   ==============================   
\*-\*-\* 00:15:30                    每年每月每天的 0 点 15 分 30 秒
Weekly                            每个周一的 00:00:00
Mon \*-\*-\* 00:00:00                同上
Mon                               同上
Wed 2020-\*-\*                      2020 年每个周三的 00:00:00
Mon..Fri 2021-\*-\*                 2021 年的每个工作日（周一到周五）的 00:00:00
2022-6,7,8-1,15 01:15:00          2022 年 6、7、8 月的 1 到 15 号的 01:15:00
Mon \*-05~03                       每年五月份的下个周一同时也是月末的倒数第三天
Mon..Fri \*-08~04                  任何年份 8 月末的倒数第四天，同时也须是工作日
\*-05~03/2                         五月末的倒数第三天，然后 2 天后再执行一次。表达式使用了波浪号（~）。
\*-05-03/2                         五月的第三天，然后每两天重复一次直到 5 月底。表达式使用了破折号（-）。
==============================   ==============================   


测试时间格式
====================================

Systemd 提供了一个绝佳的工具用于检测和测试定时器中日历时间事件的格式。``systemd-analyze calendar`` 工具解析一个时间事件格式，提供标准格式和其他有趣的信息，例如下次匹配的日期和时间，以及距离下次触发之前大概时间。

首先，看看未来没有时间的日（注意 Next elapse 和 UTC 的时间会根据你当地时区改变）:

::

    [student@studentvm1 ~]$ systemd-analyze calendar 2030-06-17
      Original form: 2030-06-17                
    Normalized form: 2030-06-17 00:00:00        
        Next elapse: Mon 2030-06-17 00:00:00 EDT
           (in UTC): Mon 2030-06-17 04:00:00 UTC
           From now: 10 years 0 months left    
    [root@testvm1 system]#

现在添加一个时间，在这个例子中，日期和时间是当作无关的部分分开解析的：

::

    [root@testvm1 system]# systemd-analyze calendar 2030-06-17 15:21:16
      Original form: 2030-06-17                
    Normalized form: 2030-06-17 00:00:00        
        Next elapse: Mon 2030-06-17 00:00:00 EDT
           (in UTC): Mon 2030-06-17 04:00:00 UTC
           From now: 10 years 0 months left    
      Original form: 15:21:16                  
    Normalized form: *-*-* 15:21:16            
        Next elapse: Mon 2020-06-15 15:21:16 EDT
           (in UTC): Mon 2020-06-15 19:21:16 UTC
           From now: 3h 55min left              
    [root@testvm1 system]#

为了把日期和时间当作一个单元来分析，可以把它们包在引号里。你在定时器单元里 OnCalendar= 时间格式中使用的时候记得把引号去掉，否则会报错：

::

    [root@testvm1 system]# systemd-analyze calendar "2030-06-17 15:21:16"
    Normalized form: 2030-06-17 15:21:16        
        Next elapse: Mon 2030-06-17 15:21:16 EDT
           (in UTC): Mon 2030-06-17 19:21:16 UTC
           From now: 10 years 0 months left    
    [root@testvm1 system]#

现在我们测试下 Table2 里的例子。我尤其喜欢最后一个：

::

    [root@testvm1 system]# systemd-analyze calendar "2022-6,7,8-1,15 01:15:00"
      Original form: 2022-6,7,8-1,15 01:15:00
    Normalized form: 2022-06,07,08-01,15 01:15:00
        Next elapse: Wed 2022-06-01 01:15:00 EDT
           (in UTC): Wed 2022-06-01 05:15:00 UTC
           From now: 1 years 11 months left
    [root@testvm1 system]#

让我们看一个例子，这个例子里我们列出了时间表达式的五个经过时间。

::

    [root@testvm1 ~]# systemd-analyze calendar --iterations=5 "Mon *-05~3"
      Original form: Mon *-05~3                
    Normalized form: Mon *-05~03 00:00:00      
        Next elapse: Mon 2023-05-29 00:00:00 EDT
           (in UTC): Mon 2023-05-29 04:00:00 UTC
           From now: 2 years 11 months left    
           Iter. #2: Mon 2028-05-29 00:00:00 EDT
           (in UTC): Mon 2028-05-29 04:00:00 UTC
           From now: 7 years 11 months left    
           Iter. #3: Mon 2034-05-29 00:00:00 EDT
           (in UTC): Mon 2034-05-29 04:00:00 UTC
           From now: 13 years 11 months left    
           Iter. #4: Mon 2045-05-29 00:00:00 EDT
           (in UTC): Mon 2045-05-29 04:00:00 UTC
           From now: 24 years 11 months left    
           Iter. #5: Mon 2051-05-29 00:00:00 EDT
           (in UTC): Mon 2051-05-29 04:00:00 UTC
           From now: 30 years 11 months left    
    [root@testvm1 ~]#

这些应该为你提供了足够的信息去开始测试你的 OnCalendar 时间格式。systemd-analyze 工具可用于其他有趣的分析，我会在这个系列的下一篇文章来探索这些。


精确时间执行任务
====================================

定时器并不会在精确的时间去执行。Systemd 定时器被故意设计成在规定时间附近随机波动的时间点触发，这样设计的初衷是为了防止多个服务在完全相同的时刻被触发。举个例子，系统管理员可以用 Weekly，Daily 等时间格式。这些快捷写法都被定义为在某一天的 00:00:00 执行。当多个定时器都这样定义的话，有很大可能它们会同时执行。

大部分情况下，这种概率抖动的定时器是没事的。当调度类似执行备份的任务，只需要它们在下班时间运行，这样是没问题的。系统管理员可以选择确定的开始时间来确保不和其他任务冲突，例如 01:05:00 这样典型的 cron 作业时间，但是有很大范围的时间值可以满足这一点。在开始时间上的一个分钟级别的随机往往是无关紧要的。

然而，对某些任务来说，精确的触发时间是个硬性要求。对于这类任务，你可以向单元文件的 Timer 块中添加如下声明来指定更高的触发时间跨度精确度（精确到微秒以内）：

::

    AccuracySec=1us

时间跨度可用于指定所需的精度，以及定义重复事件或一次性事件的时间跨度。它能识别以下单位：

- usec，us，µs
- msec，ms
- seconds，second，sec，s
- minutes，minute，min，m
- hours，hour，hr，h
- days，day，d
- weeks，week，w
- months，month，M（定义为 30.44 天）
- years，year，y（定义为 365.25 天）


运行定时器
****************************

Systemd 定时器可以被配置成根据其他 systemd 单元状态发生改变时触发。举个例子，定时器可以配置成在系统开机、启动后，或是某个确定的服务单元激活之后的一段时间被触发。这些被称为单调计时器。“单调”指的是一个持续增长的计数器或序列。这些定时器不是持久的，因为它们在每次启动后都会重置。

下表列出了一些定时器的简短定义，同时有 OnCalendar 定时器，它们被用于指定未来有可能重复的某个确定时间。详细信息请查看 systemd.timer 的手册页。

=====================    =====================
定时器                    定义
=====================    =====================
OnActiveSec=              一个与定时器被激活的那一刻相关的定时器。
OnBootSec=                一个与机器启动时间相关的计时器。
OnStartupSec=             一个与服务管理器首次启动相关的计时器，与 OnBootSec 类似，主要区别于在 Linux 中用户的服务管理器通常在首次登录后启动，而不是机器启动后。
OnUnitActiveSec=          一个与将要激活的定时器上次激活时间相关的定时器。
OnUnitInactiveSec=        一个与将要激活的定时器上次停用时间相关的定时器。
OnCalendar=               一个有日期事件表达式语法的实时（即时钟）定时器。查看 systemd.time(7) 的手册页获取更多与日历事件表达式相关的语法信息。除此以外，它的语义和 OnActiveSec= 类似。
=====================    =====================

计时器可使用同样的简写名作为它们的时间跨度，即我们之前提到的 AccuracySec 表达式，但是 systemd 将这些名字统一转换成了秒。举个例子，比如你想规定某个定时器在系统启动后五天触发一次事件；它可能看起来像 OnBootSec=5d。如果机器启动于 2020-06-15 09:45:27，这个定时器会在 2020-06-20 09:45:27 或在这之后的一分钟内触发。
