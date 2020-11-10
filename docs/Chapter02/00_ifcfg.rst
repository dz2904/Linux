CentOS 设置网口静态 IP
####################################

手动修改配置文件 ，设置对应网口的 IP 等信息。配置文件路径 ``/etc/sysconfig/network-scripts/*``


最小设置示例
************************************

.. highlight:: none

::

    # 文件名对应网口名，CentOS 8 默认配置文件
    [Linux]# vi /etc/sysconfig/network-scripts/ifcfg-eth0


    DEVICE="eth0"
    BOOTPROTO="static"
    BROADCAST="192.168.0.255"
    HWADDR="00:16:36:1B:BB:74"
    IPADDR="192.168.0.100"
    NETMASK="255.255.255.0"
    ONBOOT="yes"


参数简要解释：
 
- DEVICE    网卡的设备别名（默认即可，不建议修改）
- BOOTPROTO    获得 ip 地址的方式，常见的参数：Static（静态 ip）、dhcp（通过 dhcp 获取 ip）、bootip（通过 bootp 获取 ip）
- BROADCAST    广播地址
- HWADDR    网卡物理地址（MAC 地址，不建议修改）
- IPADDR    静态 IP 地址
- NETMASK       子网掩码
- ONBOOT    系统启动时是否激活网口

设置完成后需要重启网络服务才能生效：

::

    # CentOS 7
    [Linux]# service network restart
    或
    [Linux]# systemctl restart network.service

    # CentOS 8
    [Linux]# nmcli c reload


.. note::

    其它配置说明：
    
    - TYPE=Ethernet    网卡类型：为以太网
    - PROXY_METHOD=none    代理方式：关闭状态
    - BROWSER_ONLY=no    只是浏览器：否
    - BOOTPROTO=dhcp    网卡的引导协议
    - DEFROUTE=yes    默认路由
    - IPV4_FAILURE_FATAL=no     是否开启致命错误检测
    - IPV6INIT=yes    IPV6 是否自动初始化
    - IPV6_AUTOCONF=yes     IPV6 是否自动配置
    - IPV6_DEFROUTE=yes    IPV6 是否可以为默认路由
    - IPV6_FAILURE_FATAL=no    IPV6 是否开启致命错误检测
    - IPV6_ADDR_GEN_MODE=stable-privacy    IPV6 地址生成模型
    - NAME=etho    网络接口名称，即配置文件名后半部分。
    - UUID=f47bde51-fa78-4f79-b68f-d5dd90cfc698    通用唯一识别码
    - PREFIX=24    子网掩码


修改网关
************************************

::

    [Linux]# vi /etc/sysconfig/network

    NETWORKING=yes
    HOSTNAME=localhost.localdomain
    GATEWAY=192.168.191.2 #网关地址

查看网关命令（二选一）

::

    # GATEWAY 列为网关
    [Linux]# route -n
    Kernel IP routing table
    Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
    192.168.120.0   0.0.0.0         255.255.255.0   U     0      0        0 eth0
    192.168.0.0     192.168.120.1   255.255.0.0     UG    0      0        0 eth0
    10.0.0.0        192.168.120.1   255.0.0.0       UG    0      0        0 eth0
    0.0.0.0         192.168.120.240 0.0.0.0         UG    0      0        0 eth0
    
    
    # link src 后为网关
    [Linux]# ip route show
    default via 192.168.1.1 dev wlp82s0 proto dhcp metric 600 
    169.254.0.0/16 dev wlp82s0 scope link metric 1000 
    172.16.40.0/24 dev vmnet1 proto kernel scope link src 172.16.40.1 
    172.16.51.0/24 dev vmnet8 proto kernel scope link src 172.16.51.1 
    192.168.1.0/24 dev wlp82s0 proto kernel scope link src 192.168.1.108 metric 600 
