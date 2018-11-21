DHCP 服务配置文件
###############################

/etc/dhcpd.conf 是DHCP服务的配置文件，DHCP 服务所有参数都是通过修改 dhcpd.conf 文件来实现，安装后 dhcpd.conf 是没有做任何配置的，将 /usr/share/doc/dhcp-3.0.5/dhcpd.conf.sample 复制为 dhcpd.conf 文件进行配置：

updatedb 一下，然后执行 ``locate dhcpd.conf`` 会发现所有与 dhcpd.conf 有关的配置文件。

备份文件之后对 /etc/dhcpd.conf 文件修改，配置 DHCP 服务参数

.. highlight:: none

::

    [root@linux-a ~]# vim /etc/dhcpd.conf

    ddns-update-style interim;
    ignore client-updates;

    subnet 192.168.124.0 netmask 255.255.255.0 {

    # --- default gateway
            option routers                  192.168.124.1;
            option subnet-mask              255.255.255.0;

            option time-offset              -18000; # Eastern Standard Time
    #       option ntp-servers              192.168.1.1;
    #       option netbios-name-servers     192.168.1.1;
    # --- Selects point-to-point node (default is hybrid). Don't change this unless
    # -- you understand Netbios very well
    #       option netbios-node-type 2;

            range dynamic-bootp 192.168.124.200 192.168.124.230;
            default-lease-time 21600;
            max-lease-time 43200;

            # we want the nameserver to appear at a fixed address
            host www {
                    hardware ethernet 12:34:56:78:AB:CD;
                    fixed-address 207.175.42.254;
            }
    }


dhcpd.conf参数说明
*******************************

/etc/dhcpd.conf 通常包括三部分：parameters、declarations 、option。

1. DHCP 配置文件中的 parameters（参数）：表明如何执行任务，是否要执行任务，或将哪些网络配置选项发送给客户

ddns-update-style               配置DHCP-DNS 互动更新模式。
default-lease-time              指定确省租赁时间的长度，单位是秒。
max-lease-time                  指定最大租赁时间长度，单位是秒。
hardware                        指定网卡接口类型和MAC地址。
server-name                     通知DHCP客户服务器名称。
get-lease-hostnames flag        检查客户端使用的IP地址。
fixed-address ip                分配给客户端一个固定的地址。
authritative                    拒绝不正确的IP地址的要求。


2. DHCP 配置文件中的 declarations （声明）：用来描述网络布局、提供客户的IP地址等

shared-network                              用来告知是否一些子网络分享相同网络。
subnet                                      描述一个IP地址是否属于该子网。
range                                       起始IP 终止IP 提供动态分配IP 的范围。
host                                        主机名称 参考特别的主机。
group                                       为一组参数提供声明。
allow unknown-clients;deny unknown-client   是否动态分配IP给未知的使用者。
allow bootp;deny bootp                      是否响应激活查询。
allow booting;deny booting                  是否响应使用者查询。
filename                                    开始启动文件的名称. 应用于无盘工作站。
next-server                                设置服务器从引导文件中装如主机名，应用于无盘工作站。


3. DHCP配置文件中的option（选项）：用来配置DHCP可选参数，全部用option关键字作为开始

subnet-mask           为客户端设定子网掩码。
domain-name           为客户端指明DNS名字。
domain-name-servers   为客户端指明DNS服务器IP地址。
host-name             为客户端指定主机名称。
routers               为客户端设定默认网关。
broadcast-address     为客户端设定广播地址。
ntp-server            为客户端设定网络时间服务器IP地址。
time－offset          为客户端设定和格林威治时间的偏移时间，单位是秒。

注意：如果客户端使用的是视窗操作系统，不要选择"host-name"选项，即不要为其指定主机名称


启动和检查DHCP服务器
*******************************

使用命令启动DHCP服务器::

    #service dhcpd start

关闭DHCP服务器::

    #service dhcpd stop

重启DHCP服务器::

    #service dhcpd restart

使用ps命令检查dhcpd进程::

    #ps -ef | grep dhcpd
    root      2402     1 0 14:25 ?        00:00:00 /usr/sbin/dhcpd
    root      2764 2725 0 14:29 pts/2    00:00:00 grep dhcpd

使用检查dhcpd运行的端口::

    # netstat -nutap | grep dhcpd
    udp   0 0 0.0.0.0:67         0.0.0.0:*              2402/dhcpd

配置DHCP客户端
*********************************

通常网管员使用选择手工配置 DHCP 客户，需要修改 /etc/sysconfig/network 文件来启用联网；并修改 /etc/sysconfig/network-scripts 目录中每个网络设备的配置文件。在该目录中，每个设备都有一个叫做 ifcfg-eth？ 的配置文件，eth？是网络设备的名称。如 eth0 等。如果你想在引导时启动联网，NETWORKING 变量必须设为 yes。 除了此处之外 /etc/sysconfig/network  文件应该包含以下行：

::

    NETWORKING=yes
    DEVICE=eth0
    BOOTPROTO=dhcp
    ONBOOT=yes
