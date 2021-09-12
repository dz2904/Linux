Systemd 开机自启脚本
####################################

因为现在的 Linux 系统很多默认使用 systemd，所以网上流传的很多 init 开机自启脚本的方法已经过时。

下边介绍一种 systemd 的方法。

新建服务启动区块
****************************

Systemd 的配置文件在 ``/usr/lib/systemd/system`` 下，并且以 ``.service`` 结尾。

新建并编辑配置文件，下边是一个简单的示例：

.. highlight:: none

::

    [Unit]
    Description=Customize the  script
    After=network.target sshd-keygen.service

    [Service]
    ExecStart=/root/init.sh

    [Install]
    WantedBy=multi-user.target


Unit
====================================

[Unit] 区块通常是配置文件的第一个区块，用来定义 Unit 的元数据，以及配置与其他 Unit 的关系。它的主要字段如下。

* Description：简短描述
* Documentation：文档地址
* Requires：强依赖，如果指定的 Unit 启动失败，那么当前 Unit 会失败
* Wants：弱依赖，如果指定的 Unit 启动失败，不影响当前 Unit 启动
* Before：在指定的 Unit 启动之前，启动当前 Unit
* After：在指定的 Unit 启动之后，启动当前 Unit
* Conflicts：指定的 Unit 不能与当前 Unit 同时运行
* Condition：当前 Unit 运行时必须满足的条件，否则不会运行
* Assert：当前 Unit 运行时必须满足的条件，否则会启动失败

.. warning::

    After 和 Before 字段只涉及启动顺序，不涉及依赖关系。

    Wants 和 Requires 字段只涉及依赖关系，与启动顺序无关，默认情况下是同时启动。

当某 Web 应用需要 sql 数据库储存数据时。如果在配置文件中，只定义在 sql 之后启动 Web 服务，而没有定义强依赖 sql。上线后，如果 sql 启动失败，该 Web 应用将无法使用。


Service
====================================

[Service] 区块用来定义如何启动当前服务，只有 Service 类型的 Unit 才有这个区块。它的主要字段如下。

* Type：定义启动时的进程行为。它有以下几种值。
    * Type=simple：默认值，执行 ExecStart 指定的命令，启动主进程
    * Type=forking：以 fork 方式从父进程创建子进程，创建后父进程会立即退出
    * Type=oneshot：一次性进程，Systemd 会等当前服务退出，再继续往下执行
    * Type=dbus：当前服务通过 D-Bus 启动
    * Type=notify：当前服务启动完毕，会通知 Systemd，再继续往下执行
    * Type=idle：若有其他任务执行完毕，当前服务才会运行
* ExecStart：启动当前服务时执行的命令
* ExecStartPre：启动当前服务之前执行的命令
* ExecStartPost：启动当前服务之后执行的命令
* ExecReload：重启当前服务时执行的命令
* ExecStop：停止当前服务时执行的命令
* ExecStopPost：停止当其服务之后执行的命令
* KillMode：定义 Systemd 如何停止当前服务。
    * KillMode=control-group：当前控制组里的所有子进程，都被杀掉（默认值）
    * KillMode=process：只杀死主进程
    * KillMode=mixed：主进程将收到 SIGTERM 信号，子进程收到 SIGKILL 信号
    * KillMode=none：没有进程会被杀掉，只是执行服务的 stop 命令。
* RestartSec：自动重启当前服务间隔的秒数
* Restart：定义何种情况 Systemd 会自动重启当前服务
    * Restart=no：退出后不会重启（默认值）
    * Restart=on-success：只有正常退出时（退出状态码为 0），才会重启
    * Restart=on-failure：非正常退出时，包括被信号终止和超时，才会重启
    * Restart=on-abnormal：只有被信号终止和超时，才会重启
    * Restart=on-abort：只有在收到没有捕捉到的信号终止时，才会重启
    * Restart=on-watchdog：超时退出，才会重启
    * Restart=always：不管是什么退出原因，总是重启
* RestartSec：定义 Systemd 重启服务之前，需要等待的秒数
* TimeoutSec：定义 Systemd 停止当前服务之前等待的秒数
* Environment：指定当前服务的环境变量参数文件


.. hint::

    在所有的启动设置之前，都可以加上一个连词号 ``-`` ，表示“抑制错误”，即发生错误的时候，不影响其他命令的执行。比如 ``EnvironmentFile=-/etc/sysconfig/sshd`` ，表示即使 sshd 文件不存在，也不会抛出错误。


Install
====================================

[Install] 通常是配置文件的最后一个区块，用来定义如何启动，以及是否开机启动。它的主要字段如下。

* WantedBy：一个或多个 Target（服务组），当 Unit 激活时（开机自启动）符号链接会放入 /etc/systemd/system/ + Target 名 + .wants 后缀构成的子目录中
* RequiredBy：一个或多个 Target，当前 Unit 激活时，符号链接会放入/etc/systemd/system目录下面以 * Target 名 + .required 后缀构成的子目录中
* Alias：当前 Unit 可用于启动的别名
* Also：当前 Unit 激活（enable）时，会被同时激活的其他 Unit


.. hint::

    一般来说，常用的 Target 有两个：一个是 multi-user.target，表示多用户命令行状态；另一个是 graphical.target，表示图形用户状态，它依赖于 multi-user.target。

    可以使用 ``systemctl get-default`` 查看系统默认的启动 Target。

    ::

        [Linux]$ systemctl get-default
        graphical.target


Unit 配置文件的完整字段清单，请参考官方文档。

添加开机自启服务
****************************

1. 添加开机自动启动服务，系统会自动增加由 /lib/systemd/system/ 到 /etc/systemd/system/multi-user.target.wants/ 下的软链接。

::

    systemctl enable httpd.service

2. 移除开机自动启动服务，删除 /etc/systemd/system/multi-user.target.wants 下的软链接。

::

    systemctl disable httpd.service

3. 查看开机是否启动

::

    systemctl status httpd.service （服务详细信息）
    systemctl is-active httpd.service （仅显示是否 Active)

4. 查看开机自启动的程序

::

    ls /etc/systemd/system/multi-user.target.wants/

5. 列出所有已启动的服务

::

    systemctl list-units --type=service

6. 列出启动失败的单元

::

    systemctl --failed

7. 查看 systemd 单元加载及活动情况

::

    systemctl

8. 查看 systemd 管理的所有单元

::

    systemctl list-unit-files
