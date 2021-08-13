BT 下载工具
####################################

`Transmission <https://transmissionbt.com/>`_  是一个强大的 BitTorrent 开源客户端，实现了 BT 协议中描述的大多数功能。在官网上提供了多个版本下载，包括：Mac、GTK+、QT 版本，还有 Daemon（守护进程）版本。

Transmission 支持 DHT、Magnet Link、uTP 以及 PEX 等，最重要的是支持 Magnet Link 磁力链接下载。

Transmission 软件包含多个套件，分别是：

- transmission-gtk：GTK+ 界面的客户端
- transmission-qt：QT 界面的客户端
- transmission-cli：命令行界面的客户端，一个完整的 BT 客户端
- transmission-daemon：一个后台守护程序，本身不具备操作性，只能通过 Web 或客户端来进行控制。
- transmission-remote-cli：用来控制守护程序的客户端，只能配合 transmission-daemon 使用。


安装
************************************

安装 transmission-daemon 后台守护进程版本，此版本特别适合安装在服务器上或者嵌入式系统中，以及一些没有显示器的设备上。

.. highlight:: none

::

    [Linux]# apt install transmission-daemon

安装过程中会安装多个命令行工具：

- transmission-cli：独立的命令行客户端
- transmission-create：建立 ``.torrent`` 种子文件的命令行工具
- transmission-daemon：后台守护程序
- transmission-edit：修改 ``.torrent`` 种子文件的 announce URL
- transmission-remote：控制 daemon 的程序
- transmission-show：查看 ``.torrent`` 文件的信息


修改默认配置
************************************

默认配置文件路径：

============   ============
说明            默认文件路径
============   ============
启动脚本	    ``/etc/init.d/transmission-daemon``
配置文件	    ``/etc/default/transmission-daemon``
主配置目录	    ``/var/lib/transmsision-daemon/info``
============   ============

在主配置目录中包含如下文件：

- settings.json：主要的配置文件，设置 daemon 的各项参数。它是一个符号链接指向 ``/etc/transmission-daemon/settings.json`` 。 `官方说明 <https://github.com/transmission/transmission/wiki/Editing-Configuration-Files>`_
- torrents/：存放种子文件的目录，添加到下载任务中的种子都存放在这里。命名包含种子文件本身的名字和种子的 SHA1 HASH 值。
- resume/：存放下载信息的文件，后缀名为 ``.resume`` 。文件包含了文件中哪些部分被下载，下载的数据存储的位置等等。
- blocklists/：存储被屏蔽的 peer 地址。
- dht.dat：存储 DHT 节点信息。

.. hint:: 主配置文件各项说明

    [Linux]# vim /etc/transmission-daemon/settings.json

    {
        //下载目录
        "download-dir": "/var/lib/transmission-daemon/downloads",
        //下载队列开关
        "download-queue-enabled": true,
        //下载队列数量
        "download-queue-size": 5,
        //未完成目录
        "incomplete-dir": "/var/lib/transmission-daemon/Downloads",
        //未完成目录启用开关
        "incomplete-dir-enabled": false,
        //是否需要rpc鉴权 RPC（Remote Procedure Call）—远程过程调用
        "rpc-authentication-required": true,
        //rpc绑定地址
        "rpc-bind-address": "0.0.0.0",
        //rpc是否启用
        "rpc-enabled": true,
        //rpc主机白名单
        "rpc-host-whitelist": "",
        //rpc是否开启主机白名单
        "rpc-host-whitelist-enabled": true,
        //rpc密码
        "rpc-password": "{ffe0479ff38ffb6bbe6de697313a63a5847e41eaFgNw3iaZ",
        //rpc端口
        "rpc-port": 9091,
        //rpc访问地址
        "rpc-url": "/transmission/",
        //rpc用户名
        "rpc-username": "transmission",
        //rpc IP白名单
        "rpc-whitelist": "127.0.0.1",
        //rpc 是否开启IP白名单
        "rpc-whitelist-enabled": true,
        //速度下限（KB/s）
        "speed-limit-down": 100,
        //是否启用速度下限
        "speed-limit-down-enabled": false,
        //速度上限（KB/s）
        "speed-limit-up": 100,
        //是否启用速度上限
        "speed-limit-up-enabled": false,
        //上传数量
        "upload-slots-per-torrent": 14,
        //是否启用TP协议
        "utp-enabled": true
    }


在修改配置文件之前需要停止守护进程，否则编辑的参数将无法正确保存。

::

    [Linux]# systemctl stop transmission-daemon.service


最小修改项：

- ``rpc-username`` 设置用户名
- ``rpc-password`` 设置密码
- ``rpc-whitelist`` 设置访问 IP 白名单 

.. attention:: Web 访问时服务器提示 403 错误

    403: Forbidden
    Unauthorized IP Address.
    Either disable the IP address whitelist or add your address to it.
    If you're editing settings.json, see the 'rpc-whitelist' and 'rpc-whitelist-enabled' entries.
    If you're still using ACLs, use a whitelist instead. See the transmission-daemon manpage for details.

    根据官方文档的说明，需要将访问者 ip 加入白名单，ip 支持通配符 ``*``
    如："rpc-whitelist": "127.0.0.1, 192.168.0.*"


修改完成之后需要启动守护进程。

::

    [Linux]# systemctl start transmission-daemon.service


通过 web 访问服务
************************************

配置完成后，可以使用浏览器来访问 Transmission 服务，格式为：主机 ip（或域名）+ 端口（默认 9091），例如： ``http://192.168.0.100:9091/`` 。


客户端
************************************

嫌弃网页控制功能太少，可以使用第三方客户端进行链接。

- windows 系统推荐使用 `Transmission Remote GUI`_
- android 系统推荐使用 `Transdroid`_

.. _`Transmission Remote GUI`: https://sourceforge.net/projects/transgui/
.. _`Transdroid`: http://www.transdroid.org/
