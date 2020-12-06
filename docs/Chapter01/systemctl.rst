.. _cmd_systemctl:

systemctl 基本工具
#############################

监视和控制 systemd 的主要命令是 systemctl。该命令可用于查看系统状态和管理系统及服务。

分析系统状态
****************************

显示 系统状态：

.. highlight:: none

::

    $ systemctl status

输出激活的单元：

::

    $ systemctl

    $ systemctl list-units

输出运行失败的单元：

::

    $ systemctl --failed

查看所有已安装服务，所有可用的单元文件存放在 ``/usr/lib/systemd/system/`` 和 ``/etc/systemd/system/`` 目录（后者优先级更高）。

::

    $ systemctl list-unit-files

使用单元
****************************

一个单元配置文件可以描述如下内容之一：系统服务 ``.service`` 、挂载点 ``.mount`` 、sockets ``.sockets`` 、系统设备 ``.device`` 、交换分区 ``.swap`` 、文件路径 ``.path`` 、启动目标 ``.target`` 、由 systemd 管理的计时器 ``.timer`` 。

使用 systemctl 控制单元时，通常需要使用单元文件的全名，包括扩展名（例如 ``sshd.service`` ）。但是有些单元可以在 systemctl 中使用简写方式。

* 如果无扩展名，systemctl 默认把扩展名当作 ``.service`` 。例如 netcfg 和 netcfg.service 是等价的。
* 挂载点会自动转化为相应的 ``.mount`` 单元。例如 /home 等价于 home.mount 。
* 设备会自动转化为相应的 ``.device`` 单元，所以 /dev/sda2 等价于 dev-sda2.device 。

.. warning::

    有一些单元的名称包含一个 ``@`` 标记（例如：name@string.service ），这意味着它是模板单元 ``name@.service`` 的一个实例。 string 被称作实例标识符，在 systemctl 调用模板单元时，会将其当作一个参数传给模板单元，模板单元会使用这个传入的参数代替模板中的 ``%I`` 指示符。

    在实例化之前，systemd 会先检查 ``name@string.suffix`` 文件是否存在（如果存在，就直接使用这个文件，而不是模板实例化）。大多数情况下，包含 ``@`` 标记都意味着这个文件是模板。如果一个模板单元没有实例化就调用，该调用会返回失败，因为模板单元中的 ``%I`` 指示符没有被替换。

.. note::

    systemctl 的大部分命令都可以跟多个单元名。
    systemctl 命令在 enable、disable 和 mask 子命令中增加了 --now 选项，可以实现激活的同时启动服务，取消激活的同时停止服务。
    一个软件包可能会提供多个不同的单元。如果你已经安装了软件包，可以通过 ``pacman -Qql package | grep systemd`` 命令检查这个软件包提供了哪些单元。

立即激活单元（开启软件）：

::

    # systemctl start <单元>

立即停止单元（关闭软件）：

::

    # systemctl stop <单元>

重启单元（重启软件）：

::

    # systemctl restart <单元>

重新加载配置：

::

    # systemctl reload <单元>

输出单元运行状态：

::

    $ systemctl status <单元>

检查单元是否配置为自动启动：

::

    $ systemctl is-enabled <单元>

开机自动激活单元（开机自动启动）：

::

    # systemctl enable <单元>

设置单元为自动启动并立即启动这个单元:

::

    # systemctl enable --now unit

取消开机自动激活单元：

::

    # systemctl disable <单元>

禁用一个单元（禁用后，间接启动也是不可能的）：

::

    # systemctl mask <单元>

取消禁用一个单元：

::

    # systemctl unmask <单元>

显示单元的手册页（必须由单元文件提供）：

::

    # systemctl help <单元>

重新载入 systemd，扫描新的或有变动的单元：

::

    # systemctl daemon-reload

电源管理
****************************

安装 polkit 后才能以普通用户身份使用电源管理。

如果你正登录在一个本地的 systemd-logind 用户会话，且当前没有其它活动的会话，那么以下命令无需 root 权限即可执行。否则（例如，当前有另一个用户登录在某个 tty ）， systemd 将会自动请求输入 root 密码。

重启：

::

    $ systemctl reboot

退出系统并关闭电源：

::

    $ systemctl poweroff

待机：

::

    $ systemctl suspend

休眠：

::

    $ systemctl hibernate

混合休眠模式（同时休眠到硬盘并待机）：

::

    $ systemctl hybrid-sleep
