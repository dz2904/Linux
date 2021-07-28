wpa_supplicant 终端连接 wifi
####################################

为了管理无线网卡驱动，并且能正常连接到无线网络，你需要一个无线连接管理工具。

如何选择一个最佳的管理方法，将依赖于下面几个因素：

* 是否使用加密及使用加密的类型
* 是否经常切换不同网络（如笔记本电脑）

下表按照加密和管理方式分类，列出了管理无线网络的工具。
虽然还有其他办法，但这些是最常用的。

=====================   ================================   ===============
手动管理方法                无线连接管理                         分配 IP 地址
=====================   ================================   ===============
无加密或 WEP 加密          iw/iwconfig                         ip/dhcpcd/dhclient/networkd
WPA 或 WPA2 PSK 加密       iw/iwconfig + wpa_supplicant       ip/dhcpcd/dhclient
=====================   ================================   ===============

=================   ==================
自动管理方法           工具
=================   ==================
支持所以网络配置        netctl、Wicd、NetworkManager 等等
                      这些工具会自动安装手动配置需要的工具。
=================   ==================


wpa_supplicant 最小配置
************************************

现在大部分无线网络使用 WPA/WPA2 加密，最少需要配置 wpa_supplicant 才能连接网络。另外，wpa_supplicant 目前只能连接到已经配置好 ESSID 的无线网络。

.. note::

    其实 wpa_supplicant 还有一个前端工具 wpa_cli。wpa_supplicant 和 wpa_cli 的关系就像服务和客户端的关系，后台运行 wpa_supplicant，使用 wpa_cli 来搜索、设置、和连接网络。不过 wpa_cli 并不是必须的软件。

在 Debian 环境中，需要在 ``/etc/network/interfaces`` 中配置 wpa_supplicant 的配置文件路径。

.. highlight:: none

::

    # 开机自动启动网络并使用 dhcp 配置网络 IP
    # wlan0 为系统无线网卡名
    auto wlp1s0
    iface wlp1s0 inet dhcp

    # wpa_supplicant 的配置文件路径
    wpa-conf /etc/wpa_supplicant/wpa_supplicant.conf

新建配置文件 ``/etc/wpa_supplicant/wpa_supplicant.conf`` ，可以加入一些配置项：

::

    # 可有可无，启用 wpa_cli 关闭 wpa_supplicant
    ctrl_interface=/var/run/wpa_supplicant

    # 可有可无，只有 root 用户能读取 WPA 配置
    ctrl_interface_group=0

    # 可有可无，启用 wpa_supplicant 扫描和选择 AP
    ap_scan=1

使用 wpa_supplicant 命令将连接 wifi 的名称和密码添加到配置文件。wpa_supplicant
会将密码转换成密文，如果更改了密码这一步不能单纯的手动修改文件。

::

    [Linux]$ wpa_supplicant wifi名称  wifi密码 >> /etc/wpa_supplicant/wpa_supplicant.conf

如果配置文件中有多个 AP 还可以手动为网络设置优先级参数 priority（0-255 的整数，数值越大级别越高）。

::

    [Linux]$ cat /etc/wpa_supplicant/wpa_supplicant.conf
    network={
            ssid="WiFi2021"
            #psk="Xy20210601Xy"
            psk=c92fe5b5f76fa8482e1c131ec4e75a5fc98bebea703493732853dfa1725b058
            priority=2
    }
    network={
            ssid="glenn"
            #psk="1234@4321"
            psk=9b5b71e27b1976af9aba2b27fc8473ac095e39da62dcf497bea93449dfa9990
            priority=99
    }


下面是一个用 WPA supplicant 和 DHCP 连接无线网络的完整示例。

::

    [Linux]$ ip link set dev wlp6s0 up
    [Linux]$ wpa_supplicant -B -i wlp6s0 -c /etc/wpa_supplicant/wpa_supplicant.conf
    [Linux]$ dhcpcd wlp6s0

    要使用静态 IP，请将 dhcpcd 命令替换为：
    [Linux]$ ip addr add 192.168.0.10/24 broadcast 192.168.0.255 dev wlp6s0
    [Linux]$ ip route add default via 192.168.0.1


请注意一点，对无线网络的配置是全局性的，而非针对具体的接口。


关于 WPA：
************************************

WPA 是 WiFi Protected Access 的缩写，中文含义为“WiFi 网络安全存取”。WPA 是一种基于标准的可互操作的 WLAN 安全性增强解决方案，可大大增强现有以及未来无线局域网络的数据保护和访问控制水平。

wpa_supplicant 是 wifi 客户端（client）加密认证工具，并且是一个开源的项目，已经被移植到 Linux、Windows 以及很多嵌入式系统上。它是 WPA 的应用层认证客户端，负责完成认证相关的登录、加密等工作。

wpa_supplicant 是一个独立运行的守护进程，其核心是一个消息循环，在消息循环中处理 WPA 状态机、控制命令、驱动事件、配置信息等。
