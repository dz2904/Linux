ip 命令
###############################

Linux 的 ip 命令和 ifconfig 类似，但前者功能更强大，并旨在取代后者。使用 ip 命令，只需一个命令，你就能很轻松地执行一些网络管理任务。ifconfig 是 net-tools 中已被废弃使用的一个命令，许多年前就已经没有维护了。iproute2 套件里提供了许多增强功能的命令，ip 命令即是其中之一。


命令格式:
******************************

.. highlight:: none

::

    ip [ OPTIONS ] OBJECT { COMMAND | help }


命令功能:
******************************

ip 命令是 Linux 下较新的功能强大的网络配置工具，用来显示或配置 Linux 主机的路由、网络设备、策略路由和隧道。

命令参数:
******************************

**OPTIONS:** 是一些修改 ip 行为或者改变其输出的选项，所有的选项都是以 - 字符开头，分为长、短两种形式:

-V -Version 　　　　　　　　打印ip的版本并退出

-s -stats -statistics 　　 输出更为详尽的信息(如果这个选项出现两次或者多次，输出的信息将更为详尽)

-f -family 　　　　　　　　 这个选项后面接协议种类，包括：inet、inet6 或者 link，强调使用的协议种类。
　　　　　　　　　　　　　   如果没有足够的信息告诉ip使用的协议种类，ip就会使用默认值inet或者any。link比较特殊，它表示不涉及任何网络协议。

-4 -                      是 -family inet 的简写。

-6 -                      是 -family inet6 的简写。

-0 -                      是 -family link 的简写。

-o -oneline 　　　　　　　  对每行记录都使用单行输出，回行用字符代替。如果你需要使用 wc、grep 等工具处理 ip 的输出，会用到这个选项。

-r -resolve 　　　　　　　　查询域名解析系统，用获得的主机名代替主机 IP 地址。

**OBJECT:**  是你要管理或者获取信息的对象。ip 认识的对象包括:

link 　　　　　 网络设备

address　　　　 一个设备的协议（IP或者IPV6）地址

neighbour 　　 ARP或者NDISC缓冲区条目

route 　　　　 路由表条目

rule 　　　　  路由策略数据库中的规则

maddress 　　  多播地址

mroute 　　　  多播路由缓冲区条目

tunnel IP　　　上的通道

另外，所有的对象名都可以简写，例如：address可以简写为addr，甚至是a。

**COMMAND[ARGUMENTS]**  设置针对指定对象执行的操作

一般情况下，ip 支持对象的增加（add）、删除（delete）和展示（show或者list）。

**ARGUMENTS** 是命令的一些参数，它们倚赖于对象和命令。

ip 支持两种类型的参数：flag 和 parameter。flag 由一个关键词组成；parameter 由一个关键词加一个数值组成。

使用实例：
******************************

1. 显示网络设备的运行状态

::

    [root@localhost xinetd.d]# ip link list
    1: lo: <LOOPBACK> mtu 65536 qdisc noqueue state DOWN mode DEFAULT
        link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    2: eno16777736: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP mode DEFAULT qlen 1000
        link/ether 00:0c:29:9f:bf:8b brd ff:ff:ff:ff:ff:ff


2. 输出更为详细的网络信息

::

    [root@localhost xinetd.d]# ip -s link list
    1: lo: <LOOPBACK> mtu 65536 qdisc noqueue state DOWN mode DEFAULT
        link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
        RX: bytes  packets  errors  dropped overrun mcast
        1733956    21926    0       0       0       0
        TX: bytes  packets  errors  dropped carrier collsns
        1733956    21926    0       0       0       0
    2: eno16777736: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP mode DEFAULT qlen 1000
        link/ether 00:0c:29:9f:bf:8b brd ff:ff:ff:ff:ff:ff
        RX: bytes  packets  errors  dropped overrun mcast
        790788625  551713   0       0       0       0
        TX: bytes  packets  errors  dropped carrier collsns
        19844418   287915   0       0       0       0

3. 显示核心路由表

::

    [root@localhost xinetd.d]# ifconfig
    eno16777736: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
            inet 192.168.0.15  netmask 255.255.255.0  broadcast 192.168.0.255
            inet6 fe80::20c:29ff:fe9f:bf8b  prefixlen 64  scopeid 0x20<link>
            ether 00:0c:29:9f:bf:8b  txqueuelen 1000  (Ethernet)
            RX packets 556578  bytes 791923646 (755.2 MiB)
            RX errors 0  dropped 0  overruns 0  frame 0
            TX packets 294987  bytes 20580554 (19.6 MiB)
            TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

    [root@localhost xinetd.d]# ip route list
    default via 192.168.0.1 dev eno16777736  proto static  metric 100
    192.168.0.0/24 dev eno16777736  proto kernel  scope link  src 192.168.0.15  metric 100

4. 显示邻居表

::

    [root@localhost xinetd.d]# ip neigh list
    192.168.0.1 dev eno16777736 lladdr 70:62:b8:bd:8c:d6 REACHABLE


5. 查看网卡信息

::

    [root@localhost xinetd.d]# ip -s link list eno16777736　　　　　　//等价于ifconfig eno16777736，（单网卡时等价于ifconfig）
    2: eno16777736: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP mode DEFAULT qlen 1000
        link/ether 00:0c:29:9f:bf:8b brd ff:ff:ff:ff:ff:ff
        RX: bytes  packets  errors  dropped overrun mcast
        808213913  570857   0       0       0       0
        TX: bytes  packets  errors  dropped carrier collsns
        21503126   305593   0       0       0       0
    [root@localhost xinetd.d]# ifconfig eno16777736　　　　　　
    eno16777736: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
            inet 192.168.0.15  netmask 255.255.255.0  broadcast 192.168.0.255
            inet6 fe80::20c:29ff:fe9f:bf8b  prefixlen 64  scopeid 0x20<link>
            ether 00:0c:29:9f:bf:8b  txqueuelen 1000  (Ethernet)
            RX packets 572918  bytes 811238488 (773.6 MiB)
            RX errors 0  dropped 0  overruns 0  frame 0
            TX packets 306676  bytes 21578727 (20.5 MiB)
            TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0　

#. 自定义 IP 地址

::

    [root@localhost xinetd.d]# ip addr add 192.1.1.1/24 dev ens33      //自定义ip地址，ens33 为网卡硬件名
    [root@localhost xinetd.d]# ip addr del 192.1.1.1/24 dev ens33      //删除自定义

#. 改变设备参数信息

::

    [root@localhost xinetd.d]# ip link set dev eno16777736 txqueuelen 1200　　　　　　//改变最大传输队列长度
    [root@localhost xinetd.d]# ip link set dev eno16777736 MTU 1600　　　　　　　　　　//MTU大写不行
    Error: either "dev" is duplicate, or "MTU" is a garbage.
    [root@localhost xinetd.d]# ip link set dev eno16777736 mtu 1600　　　　　　　　　　//改变网络设备最大传输单元的值
    [root@localhost xinetd.d]# ifconfig eno16777736
    eno16777736: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1600
            ether 00:0c:29:9f:bf:8b  txqueuelen 1200  (Ethernet)
            RX packets 587649  bytes 832219280 (793.6 MiB)
            RX errors 0  dropped 0  overruns 0  frame 0
            TX packets 315027  bytes 22173716 (21.1 MiB)
            TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0


#. 设置网络设备为不可用状态

::

    [root@localhost xinetd.d]# ip link set eno16777736 down　　　　　　　　　　//关闭网卡
    [root@localhost xinetd.d]# ip link set eno16777736 up               　　 //开启网卡
    [root@localhost xinetd.d]# ifup eno16777736　　　　　　　　　　　　　　　　　//激活连接
    成功激活的连接（D-Bus 激活路径：/org/freedesktop/NetworkManager/ActiveConnection/13）
    [root@localhost xinetd.d]# ifdown eno16777736
    Device 'eno16777736' successfully disconnected.　　　　　　　　　　　　　　 //关闭连接


#. 显示协议地址(网卡信息)

::

    [root@localhost xinetd.d]# ip addr ls eno16777736
    2: eno16777736: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1600 qdisc pfifo_fast state UP qlen 1200
        link/ether 00:0c:29:9f:bf:8b brd ff:ff:ff:ff:ff:ff
    [root@localhost xinetd.d]# ip link list
    1: lo: <LOOPBACK> mtu 65536 qdisc noqueue state DOWN mode DEFAULT
        link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    2: eno16777736: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1600 qdisc pfifo_fast state UP mode DEFAULT qlen 1200
        link/ether 00:0c:29:9f:bf:8b brd ff:ff:ff:ff:ff:ff

#. 类似于 ifconfig 命令的输出

::

    [root@localhost xinetd.d]# ip addr


