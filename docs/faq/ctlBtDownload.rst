命令行环境下的 BT 下载
#############################

第一步：安装 transmission-daemon 版本，这样才能够让 transmission 进程在后台执行。

::

    root@raspberrypi:~# aptitude install transmission-daemon
    Reading package lists... Done
    Building dependency tree
    Reading state information... Done
    The following additional packages will be installed:
      libminiupnpc10 libnatpmp1 minissdpd transmission-cli transmission-common
    Suggested packages:
      natpmp-utils transmission-gtk
    The following NEW packages will be installed:
      libminiupnpc10 libnatpmp1 minissdpd transmission-cli transmission-common transmission-daemon
    0 upgraded, 6 newly installed, 0 to remove and 0 not upgraded.
    Need to get 799 kB of archives.
    After this operation, 3,516 kB of additional disk space will be used.
    Do you want to continue? [Y/n] y
    ...

第二步：修改配置文件

官方说明：https://github.com/transmission/transmission/wiki/Editing-Configuration-Files

::

    root@raspberrypi:~# vim /etc/transmission-daemon/settings.json

    对应翻译：
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

第三步：执行加载配置文件和重启服务操作

::

    root@raspberrypi:~# service transmission-daemon reload
    root@raspberrypi:~# service transmission-daemon restart

    # 启动 Transmission 进程
    root@raspberrypi:~# systemctl restart transmission-daemon.service


第四步：通过 web 查看 Transmission 服务

我们可以使用浏览器来访问 Transmission 服务，格式为：主机ip（或域名）+端口（默认9091）。

例如：http://192.168.0.100:9091/

::

    访问时服务器提示 403 错误

    403: Forbidden
    Unauthorized IP Address.
    Either disable the IP address whitelist or add your address to it.
    If you're editing settings.json, see the 'rpc-whitelist' and 'rpc-whitelist-enabled' entries.
    If you're still using ACLs, use a whitelist instead. See the transmission-daemon manpage for details.

    根据官方文档的说明，需要将访问者 ip 加入白名单，ip 支持通配符 ``*`` ，如："rpc-whitelist": "127.0.0.1, 192.168.0.*"

成功连接后会提示输入用户名密码，分别是配置文件中 ``rpc-username`` 和 ``rpc-password`` 字段的值。

附加项：客户端

嫌弃网页控制功能太少，可以使用第三方客户端进行链接。

* windows 系统推荐使用 `Transmission Remote GUI`_

.. _`Transmission Remote GUI`: https://sourceforge.net/projects/transgui/

* android 系统推荐使用 `Transdroid`_

.. _`Transdroid`: http://www.transdroid.org/
