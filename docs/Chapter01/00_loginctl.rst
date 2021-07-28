.. _cmd_loginctl:

loginctl 查看用户
####################################

loginctl 命令可用于检查和控制 systemd 的状态；查看已经登录的用户会话消息。


命令格式：
************************************

.. highlight:: none

::

    loginctl [OPTIONS...] {COMMAND} ...


常用选项：
************************************

::

    -a, --all
        显示全部属性，无论属性是否已经被设置

    -l, --full
        在显示进程树的时候，不对超长行进行截断

    -n, --lines=
        显示多少行日志。必须为正整数，默认值"10"


    list-sessions
        列出当前所有的会话。这是默认命令

    session-status [ID…]

        显示简洁的会话状态信息， 后跟最近的日志。
        如果指定了会话ID，那么仅显示指定的会话， 否则显示当前调用者的会话。
        此命令仅用于输出人类易读的信息， 如果你想输出易于程序分析的信息，
        那么应该使用 show-session 命令

    terminate-user USER…
        结束指定用户的所有会话。 这将杀死该用户的所有会话中的所有进程， 同时释放与此用户有关的所有资源。

    kill-user USER…
        向指定用户的所有进程发送 --signal= 选项指定的信号。 


    list-seats
        列出当前本机上的 所有可用席位

    seat-status [NAME…]
        显示简洁的席位信息，后跟最近的日志。
        如果指定了席位名，那么仅显示指定的席位， 否则显示当前调用者会话所属的席位。
        此命令仅用于输出人类易读的信息， 如果你想输出易于程序分析的信息，
        那么应该使用 show-seat 命令。

    terminate-seat NAME…
        结束指定席位上的所有会话。 这将杀死指定席位上的所有会话进程， 同时释放与之关联的所有资源。


使用实例：
************************************

::
    
    # 列出本机所有的 session
    # 不带参数执行 loginctl 和执行 loginctl list-sessions 效果一样
    [Linux]$ loginctl
    SESSION  UID USER SEAT TTY
      1 1000 xiao      pts/0
      5 1000 xiao      pts/1

    2 sessions listed.


    # 查看 session 的详细信息
    [Linux]$ loginctl show-session 5
    EnableWallMessages=no
    NAutoVTs=6
    KillUserProcesses=no
    RebootToFirmwareSetup=no
    IdleSinceHint=1627474393034083
    UserStopDelayUSec=10s
    HandlePowerKey=poweroff
    IdleAction=ignore
    PreparingForShutdown=no
    Docked=no
    NCurrentSessions=2
    ...


    # 杀死 session
    [Linux]$ loginctl kill-session 976

    
    # 查看登录用户的详细信息
    [Linux]$ loginctl show-user 1000
    UID=1000
    GID=1000
    Name=xiao
    Timestamp=Wed 2021-07-28 18:53:32 CST
    RuntimePath=/run/user/1000
    Slice=user-1000.slice
    ...


    # 查看登录用户的状态
    [Linux]$ loginctl user-status xiao
    xiao (1000)
               Since: Wed 2021-07-28 18:53:32 CST; 1h 25min ago
               State: active
            Sessions: 5 *1
              Linger: no
                Unit: user-1000.slice
                      ├─session-1.scope
                      │ ├─730 sshd: xiao [priv]
                      │ ├─747 sshd: xiao@pts/0
                      │ ├─748 -bash
                      │ └─851 vim 00_loginctl.rst
                      ├─session-5.scope
                      │ ├─852 sshd: xiao [priv]
                      │ ├─858 sshd: xiao@pts/1
                      │ ├─859 -bash
                      │ ├─862 su
                      │ ├─863 bash
                      │ ├─934 loginctl user-status xiao
                      │ └─935 pager
                      └─user@1000.service
                        └─init.scope
                          ├─733 /lib/systemd/systemd --user
                          └─734 (sd-pam)

    Jul 28 18:53:33 debian systemd[733]: Listening on GnuPG cryptographic agent and passphrase
    Jul 28 18:53:33 debian systemd[733]: Listening on GnuPG cryptographic agent (ssh-agent emu
    ...
