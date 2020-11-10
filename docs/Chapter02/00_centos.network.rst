CentOS 网络配置
####################################

在 RHEL 或者 CentOS 等 Redhat 系的系统中，跟网络有关的配置文件如下：

- /etc/host.conf 配置域名服务客户端的控制文件
- /etc/hosts  完成主机名映射为IP地址的功能
- /etc/resolv.conf 域名服务客户端的配置文件,用于指定域名服务器的位置
- /etc/sysconfig/network 包含了主机最基本的网络信息,用于系统启动
- /etc/sysconfig/network-script/  系统启动时初始化网络的一些信息
- /etc/xinetd.conf 定义了由超级进程 xinetd 启动的网络服务
- /etc/networks 完成域名与网络地址的映射
- /etc/protocols 设定了主机使用的协议以及各个协议的协议号
- /etc/services 设定主机的不同端口的网络服务


.. highlight:: none

::

[Linux]# vi /etc/sysconfig/network-scripts/eth0


网络故障排除思路
 ***********************************
 
1. 检查硬件，网口灯是否亮起，网线是否畅通
2. 检查配置文件是否有错误，书写及语法错误等
3. 检查本机网络协议是否正确： ping 127.0.0.1
4. 检查本机网卡链路是否正确： ping 本机IP地址
5. 检查网关是否正确： ping 网关IP地址
6. 检查外部连通性： ping www.baidu.com

