wpa_supplicant 终端连接 wifi
##################################

为了管理已经安装好的无线驱动，并且能正常工作连接无线网络，需要安装一个无线连接管理工具。

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
支持网络配置           netctl、Wicd、NetworkManager 等等
                      这些工具会自动安装手动配置需要的工具。
=================   ==================

现在大部分无线网络使用 WPA/WPA2 加密，所以需要配置 wpa_supplicant。

在 wpa_supplicant 源码目录下，有一个参考的配置文件 wpa_supplicant.conf，几乎包含了所有的配置项。
我们的配置文件不需要这么复杂，只需在无线网络启动后自动连接到预先设置的 ap 就可以了。如果预先设置了多个 ap 则需要为网络设置优先级参数 priority（0-255 的整数，数值越大，级别越高）。

新建配置文件 ``/etc/wpa_supplicant/wpa_supplicant.conf`` ，内容如下：

.. highlight:: none

::

    ctrl_interface=/var/run/wpa_supplicant

    update_config=1

    network={

    ssid="LZ205"   # wifi 名称
    psk="20100208"   # 密码
    priority=2

    }


下面是一个用 WPA supplicant 和 DHCP 连接无线网络的完整示例。

::

    # ip link set dev wlp6s0 up
    # wpa_supplicant -B -i wlp6s0 -c /etc/wpa_supplicant/wpa_supplicant.conf
    # dhcpcd wlp6s0

    要使用静态 IP，请将 dhcpcd 命令替换为：
    # ip addr add 192.168.0.10/24 broadcast 192.168.0.255 dev wlp6s0
    # ip route add default via 192.168.0.1


请注意一点，对无线网络的配置是全局性的，而非针对具体的接口。

关于 WPA：
######################

WPA 是 WiFi Protected Access 的缩写，中文含义为“WiFi 网络安全存取”。WPA 是一种基于标准的可互操作的 WLAN 安全性增强解决方案，可大大增强现有以及未来无线局域网络的数据保护和访问控制水平。

wpa_supplicant 是 wifi 客户端（client）加密认证工具，并且是一个开源的项目，已经被移植到 Linux、Windows 以及很多嵌入式系统上。它是 WPA 的应用层认证客户端，负责完成认证相关的登录、加密等工作。

wpa_supplicant 是一个独立运行的守护进程，其核心是一个消息循环，在消息循环中处理 WPA 状态机、控制命令、驱动事件、配置信息等。

wpa_supplicant 源程序有两个主要的可执行工具：wpa_supplicant 和 wpa_cli。
wpa_supplicant 是核心程序，它和 wpa_cli 的关系就是服务和客户端的关系。
wpa_supplicant 在后台运行，可以使用 wpa_cli 命令来搜索、设置和连接网络。
