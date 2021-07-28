.. _cmd_systemctl:

systemctl 基本工具
####################################

systemctl 是一个 :ref:`systemd <cfg_systemd>`  工具，主要负责控制 systemd 系统和服务管理器。

systemctl 命令有两大类功能：

- 控制 systemd 系统
- 管理系统上运行的服务（Unit 单元）


命令格式：
************************************

.. highlight:: none

::

    systemctl [OPTIONS...] {COMMAND} ...


常用选项：
************************************

::

    -h --help
        显示帮助

    -t --type=TYPE
        显示指定类型的单元
      
    -p --property=NAME
        仅显示指定名称的属性

    -l --full
        不要在输出中省略单元名称

    -s --signal=SIGNAL
        指定发送信号

    -q --quiet
        抑制输出

    -n --lines=INTEGER
        显示的日志条目数


Unit 命令
++++++++++++++++++++++++++++++++++++

::

    list-units [PATTERN...]
        列出当前在内存中的单元

    list-sockets [PATTERN...]
        列出当前在内存中的套接字单元，按地址排序

    list-timers [PATTERN...]
        列出当前在内存中的计时器单元，按下一次的顺序排列

    start UNIT...
        启动(激活)一个或多个单位

    stop UNIT...
        停止(关闭)一个或多个单元

    restart UNIT...
        启动或重启一个或多个单元

    reload UNIT...
        重新加载一个或多个单元的配置文件

    status UNIT...
        查看单元的运行状态

    show [PATTERN...|JOB...]  
        显示一个或多个单元/进程的配置文件参数

    isolate UNIT
        启动一个单元，停止所有其他单元

    mask UNIT
        禁用一个单元（禁用后，也不可以间接启动）：

    unmask UNIT
        取消禁用一个单元

    kill UNIT...
        杀死（结束）一个或多个单元的所有子进程

    list-dependencies [UNIT]
        递归地显示单元所需要的单元


Unit 文件命令
++++++++++++++++++++++++++++++++++++

::

    list-unit-files [PATTERN...]
        列出已安装的单元文件

    enable [UNIT...|PATH...]
        启用一个或多个单元文件

    disable UNIT...
        禁用一个或多个单元文件

    reenable UNIT...
        重新启用一个或多个单元文件

    is-enabled UNIT...
        检查单元文件是否启用

    edit UNIT...
        编辑一个或多个单元文件


系统命令
++++++++++++++++++++++++++++++++++++

::

    default
        进入系统默认模式

    rescue
        进入系统救援模式

    emergency
        进入系统应急模式

    poweroff
        退出系统并关闭电源

    reboot [ARG]
        重新启动系统

    suspend
        挂起（待机）系统

    hibernate
        休眠系统

    hybrid-sleep 
        混合休眠模式（硬盘同时休眠并待机）


systemctl 的大部分命令后都可以跟多个单元名。 systemctl 命令在 enable、disable 和 mask 子命令中增加了 --now 选项，可以实现激活的同时启动服务，取消激活的同时停止服务。

使用实例：
************************************

::

    # 立即激活单元（开启软件）：
    [Linux]# systemctl start <单元>


    # 立即停止单元（关闭软件）：
    [Linux]# systemctl stop <单元>


    # 重启单元（重启软件）：
    [Linux]# systemctl restart <单元>


    # 输出单元运行状态：
    [Linux]# systemctl status nginx.service
    ● nginx.service - A high performance web server and a reverse proxy server
       Loaded: loaded (/lib/systemd/system/nginx.service; enabled; vendor preset: enabled)
       Active: active (running) since Wed 2021-07-28 18:52:28 CST; 3h 50min ago
         Docs: man:nginx(8)
      Process: 711 ExecStartPre=/usr/sbin/nginx -t -q -g daemon on; master_process on; (code=e
      Process: 716 ExecStart=/usr/sbin/nginx -g daemon on; master_process on; (code=exited, st
     Main PID: 717 (nginx)
        Tasks: 2 (limit: 515)
       Memory: 10.0M
       CGroup: /system.slice/nginx.service
               ├─717 nginx: master process /usr/sbin/nginx -g daemon on; master_process on;
               └─718 nginx: worker process


    # 查看单元的配置参数
    [Linux]# systemctl show nginx
    Type=forking
    Restart=no
    PIDFile=/run/nginx.pid
    NotifyAccess=none
    RestartUSec=100ms
    TimeoutStartUSec=1min 30s
    TimeoutStopUSec=5s
    RuntimeMaxUSec=infinity
    ExecMainStatus=0
    ...


    # 检查单元是否配置为自动启动：
    [Linux]# systemctl is-enabled nginx
    enabled


    # 设置单元为自动启动并立即启动这个单元:
    [Linux]# systemctl enable --now nginx


    # 重启系统：
    [Linux]# systemctl reboot


    # 退出系统并关闭电源：
    [Linux]# systemctl poweroff
