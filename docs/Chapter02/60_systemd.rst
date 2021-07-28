Systemd 进程管理工具
############################

历史上，Linux 的启动管理一直采用 init 进程，但是 init 有两个明显的缺点：

1. 启动时间长。init 进程是串行启动，只有前一个进程启动完，才会启动下一个进程。容易出现阻塞，并且开机时间较长。

2. 启动脚本复杂。init 进程只是执行启动脚本，不管其他事情。脚本需要自己处理各种情况，这往往使得脚本变得很长。

Systemd 就是为了解决这些问题而诞生的。它的设计目标是，为系统的启动和管理提供一套完整的解决方案。现在 Systemd 已经取代了 initd 成为了大多数发行版的标准配置，Systemd 为系统的第一个进程（PID 等于 1），其他进程都是它的子进程。

Systemd 的优点是功能强大，使用方便，缺点是体系庞大，非常复杂。事实上，现在还有很多人反对使用 Systemd，理由就是它过于复杂，与操作系统的其他部分强耦合，违反 "keep simple, keep stupid" 的 Unix 哲学。

Unit
****************************

systemd 所管理的所有系统资源都称作 Unit（单位），通过 systemd 命令集可以方便的管理 Unit。

Unit 一共分为 12 种。

- Service unit：系统服务
- Target unit：多个 Unit 构成的一个组
- Device Unit：硬件设备
- Mount Unit：文件系统的挂载点
- Automount Unit：自动挂载点
- Path Unit：文件或路径
- Scope Unit：不是由 Systemd 启动的外部进程
- Slice Unit：进程组
- Snapshot Unit：Systemd 快照，可以切回某个快照
- Socket Unit：进程间通信的 socket
- Swap Unit：swap 文件
- Timer Unit：定时器

``systemctl list-units`` 命令可以查看当前系统的所有 Unit 。

.. highlight:: none

::

    # 列出正在运行的 Unit
    [Linux]# systemctl list-units

    # 列出所有 Unit，包括没有找到配置文件的或者启动失败的
    [Linux]# systemctl list-units --all

    # 列出所有没有运行的 Unit
    [Linux]# systemctl list-units --all --state=inactive

    # 列出所有加载失败的 Unit
    [Linux]# systemctl list-units --failed

    # 列出所有正在运行的、类型为 service 的 Unit
    [Linux]# systemctl list-units --type=service


``systemctl status`` 命令用于查看系统状态和单个 Unit 的状态。

::

    # 显示系统状态
    [Linux]# systemctl status

    # 显示单个 Unit 的状态
    [Linux]# sysystemctl status bluetooth.service


对于用户来说，最常用的是下面这些命令，用于启动和停止 Unit（主要是 service）。

::

    # 立即启动一个服务
    [Linux]# sudo systemctl start apache.service

    # 立即停止一个服务
    [Linux]# sudo systemctl stop apache.service

    # 重启一个服务
    [Linux]# sudo systemctl restart apache.service

    # 杀死一个服务的所有子进程
    [Linux]# sudo systemctl kill apache.service

    # 重新加载一个服务的配置文件
    [Linux]# sudo systemctl reload apache.service

    # 重载所有修改过的配置文件
    [Linux]# sudo systemctl daemon-reload

    # 显示某个 Unit 的所有底层参数
    [Linux]# systemctl show httpd.service


Unit 配置文件
****************************

每一个 Unit 都有一个配置文件，告诉 Systemd 怎么启动这个 Unit 。

Systemd 默认从目录 ``/etc/systemd/system/`` 读取配置文件。但是，里面存放的大部分文件都是符号链接，指向目录 ``/usr/lib/systemd/system/`` ，真正的配置文件存放在那个目录。

==============================       =============================
文件说明                              路径
==============================       =============================
服务启动的脚本启动路径	              /usr/lib/systemd/system
开机自启服务存放路径                  /etc/systemd/system/multi-user.target.wants/
默认运行级别配置文件                  /etc/systemd/system/default.target
==============================       =============================

.. note::

    其实 /usr/lib/systemd/system/ 目录中的文件是 /lib/systemd/system 目录下文件的硬链接，可以使用 ``ls -li`` 命令查看对比两个文件。


``systemctl enable`` 命令用于在上面两个目录之间，建立符号链接关系。

::

    [Linux]# systemctl enable sshd.service
    # 等同于
    [Linux]# ln -s '/usr/lib/systemd/system/sshd.service' '/etc/systemd/system/multi-user.target.wants/sshd.service'

如果配置文件里面设置了开机启动， ``systemctl enable`` 命令相当于激活开机启动。

与之对应的， ``systemctl disable`` 命令用于在两个目录之间，撤销符号链接关系，相当于撤销开机启动。

配置文件的后缀名默认为 ``.service`` ，比如 sshd.socket。可以省略后缀名，所以 sshd 会被理解成 sshd.service。

``systemctl list-unit-files`` 命令用于列出所有配置文件。

::

    # 列出所有配置文件，命令输出一个列表。
    $ systemctl list-unit-files

    UNIT FILE              STATE
    chronyd.service        enabled
    clamd@.service         static
    clamd@scan.service     disabled


这个列表显示每个配置文件的状态，一共有四种。

- enabled：已建立启动链接
- disabled：没建立启动链接
- static：该配置文件没有[Install]部分（无法执行），只能作为其他配置文件的依赖
- masked：该配置文件被禁止建立启动链接

注意，从配置文件的状态无法看出，该 Unit 是否正在运行。这必须执行前面提到的 ``systemctl status`` 命令。修改 Unit 配置文件后，需要重新加载配置文件，然后重新启动，否则修改不会生效。


Target
****************************

启动计算机的时候，需要启动大量的 Unit。如果每一次启动，都要一一写明本次启动需要哪些 Unit，显然非常不方便。Systemd 的解决方案就是 Target。

简单说，Target 就是一个 Unit 组，包含许多相关的 Unit 。启动某个 Target 的时候，Systemd 就会启动里面所有的 Unit。从这个意义上说，Target 这个概念类似于"状态点"，启动某个 Target 就好比启动到某种状态。

传统的init启动模式里面，有 RunLevel 的概念，跟 Target 的作用很类似。不同的是，RunLevel 是互斥的，不可能多个 RunLevel 同时启动，但是多个 Target 可以同时启动。

::

    # 查看当前系统的所有 Target
    $ systemctl list-unit-files --type=target

    # 查看一个 Target 包含的所有 Unit
    $ systemctl list-dependencies multi-user.target

    # 查看启动时的默认 Target
    $ systemctl get-default

    # 设置启动时的默认 Target
    $ sudo systemctl set-default multi-user.target

    # 切换 Target 时，默认不关闭前一个 Target 启动的进程，
    # systemctl isolate 命令改变这种行为，
    # 关闭前一个 Target 里面所有不属于后一个 Target 的进程
    $ sudo systemctl isolate multi-user.target


日志管理
****************************

Systemd 统一管理所有 Unit 的启动日志。带来的好处就是，可以只用journalctl一个命令，查看所有日志（内核日志和应用日志）。日志的配置文件是/etc/systemd/journald.conf。

journalctl功能强大，用法非常多。

::

    # 查看所有日志（默认情况下 ，只保存本次启动的日志）
    $ sudo journalctl

    # 查看内核日志（不显示应用日志）
    $ sudo journalctl -k

    # 查看系统本次启动的日志
    $ sudo journalctl -b
    $ sudo journalctl -b -0

    # 查看上一次启动的日志（需更改设置）
    $ sudo journalctl -b -1

    # 查看指定时间的日志
    $ sudo journalctl --since="2012-10-30 18:17:16"
    $ sudo journalctl --since "20 min ago"
    $ sudo journalctl --since yesterday
    $ sudo journalctl --since "2015-01-10" --until "2015-01-11 03:00"
    $ sudo journalctl --since 09:00 --until "1 hour ago"

    # 显示尾部的最新10行日志
    $ sudo journalctl -n

    # 显示尾部指定行数的日志
    $ sudo journalctl -n 20

    # 实时滚动显示最新日志
    $ sudo journalctl -f

    # 查看指定服务的日志
    $ sudo journalctl /usr/lib/systemd/systemd

    # 查看指定进程的日志
    $ sudo journalctl _PID=1

    # 查看某个路径的脚本的日志
    $ sudo journalctl /usr/bin/bash

    # 查看指定用户的日志
    $ sudo journalctl _UID=33 --since today

    # 查看某个 Unit 的日志
    $ sudo journalctl -u nginx.service
    $ sudo journalctl -u nginx.service --since today

    # 实时滚动显示某个 Unit 的最新日志
    $ sudo journalctl -u nginx.service -f

    # 合并显示多个 Unit 的日志
    $ journalctl -u nginx.service -u php-fpm.service --since today

    # 查看指定优先级（及其以上级别）的日志，共有8级
    # 0: emerg
    # 1: alert
    # 2: crit
    # 3: err
    # 4: warning
    # 5: notice
    # 6: info
    # 7: debug
    $ sudo journalctl -p err -b

    # 日志默认分页输出，--no-pager 改为正常的标准输出
    $ sudo journalctl --no-pager

    # 以 JSON 格式（单行）输出
    $ sudo journalctl -b -u nginx.service -o json

    # 以 JSON 格式（多行）输出，可读性更好
    $ sudo journalctl -b -u nginx.serviceqq
     -o json-pretty

    # 显示日志占据的硬盘空间
    $ sudo journalctl --disk-usage

    # 指定日志文件占据的最大空间
    $ sudo journalctl --vacuum-size=1G

    # 指定日志文件保存多久
    $ sudo journalctl --vacuum-time=1years


Systemd 命令集合
****************************

Systemd 并不是一个命令，而是一组命令，涉及到系统管理的方方面面。

systemctl
++++++++++++++++++++++++++++++++

systemctl 是 Systemd 的主命令，用于管理系统。

::

    # 重启系统
    [Linux]# systemctl reboot

    # 关闭系统，切断电源
    [Linux]# systemctl poweroff

    # 让系统进入冬眠状态
    [Linux]# systemctl hibernate

    # 让系统进入交互式休眠状态
    [Linux]# systemctl hybrid-sleep

    # 启动进入救援状态（单用户状态）
    [Linux]# systemctl rescue

    # 列出所有已启动的服务
    systemctl list-units --type=service

    # 查看 systemd 管理的所有单元
    systemctl list-unit-files


systemd-analyze
++++++++++++++++++++++++++++++++

systemd-analyze 命令用于查看启动耗时。

::

    # 查看启动耗时
    [Linux]# systemd-analyze

    # 查看每个服务的启动耗时
    [Linux]# systemd-analyze blame

    # 显示瀑布状的启动过程流
    [Linux]# systemd-analyze critical-chain

    # 显示指定服务的启动流
    [Linux]# systemd-analyze critical-chain atd.service


hostnamectl
++++++++++++++++++++++++++++++++

hostnamectl 命令用于查看当前主机的信息。

::

    # 显示当前主机的信息
    [Linux]# hostnamectl

    # 设置主机名。
    [Linux]# hostnamectl set-hostname rhel7


localectl
++++++++++++++++++++++++++++++++

localectl 命令用于查看本地化设置。

::

    # 查看本地化设置
    [Linux]# localectl

    # 设置本地化参数。
    [Linux]# sudo localectl set-locale LANG=en_GB.utf8
    [Linux]# sudo localectl set-keymap en_GB


timedatectl
++++++++++++++++++++++++++++++++

timedatectl 命令用于查看当前时区设置。

::

    # 查看当前时区设置
    [Linux]# timedatectl

    # 显示所有可用的时区
    [Linux]# timedatectl list-timezones

    # 设置当前时区
    [Linux]# timedatectl set-timezone America/New_York
    [Linux]# timedatectl set-time YYYY-MM-DD
    [Linux]# timedatectl set-time HH:MM:SS

loginctl
++++++++++++++++++++++++++++++++

loginctl 命令用于查看当前登录的用户。

::

    # 列出当前 session
    [Linux]# loginctl list-sessions

    # 列出当前登录用户
    [Linux]# loginctl list-users

    # 列出显示指定用户的信息
    [Linux]# loginctl show-user ruanyf


