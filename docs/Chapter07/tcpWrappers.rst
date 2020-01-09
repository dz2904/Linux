tcp_wrappers
################################

Linux 系统下的防火墙主要分为两层：第一层是对 IP 进行过滤的 iptables，第二层就是 tcp_wrappers。

TCP Wrappers（也称为 tcp_wrappers，全称 Transmission Control Protocol Wrappers）是一个基于主机的网络 ACL 系统，用于过滤对（类Unix）操作系统上的 Internet 协议服务的网络访问。

原始代码由 Wietse Venema 于 1990 年编写，用于监控在埃因霍温科技大学数学与计算机科学系的 Unix 工作站上的破解者活动，并于 2001 年 6 月 1 日以自己的 BSD 式许可证发布。

tarball 包含一个名为 libwrap 的库，它实现了实际的功能。最初，只有使用 tcpd 程序将来自超级服务器（例如 inetd）的每个连接生成的服务包装起来。但是，今天最常见的网络服务守护进程可以直接链接到 libwrap。守护进程直接使用它而不是从超级服务器生成，或者当单个进程处理多个连接时运行。

与守护进程配置文件中经常出现的主机访问控制指令相比，TCP Wrappers 具有运行 ACL 时重新配置的优势（即不必重新加载或重新启动服务）。


有两种方式来判断一个服务是否支持 tcp wrapper：

1. 通过查找库文件看是否有 libwrap

.. highlight:: none

::

    ldd `which command`

2.查看是否连接到 /etc/hosts.allow|deny

::

    strings `which command` # 查看静态链接库
    如果有/etc/hosts.allow
    /etc/hosts.deny # 就说明这个命令静态链接了 tcpwrraper

tcpwrraper 自身工作在内核，却可以通过这两个配置文件控制， ``/etc/hosts.allow`` 控制可以访问本机的 IP 地址， ``/etc/hosts.deny`` 控制禁止访问本机的 IP 地址。如果两个文件的配置有冲突，以 ``/etc/hosts.deny`` 为准。

hosts.allow 和 hosts.deny 文件中的每行代码都遵循以下格式::

    daemon_list client_list [command]

其中，daemon_list 是一个或多个服务器 daemon 程序（如rpcbind、vsftpd、或sshd）的列表（用逗号分隔）。client_list 是一个或者多个客户端的列表（用逗号分隔）。命令是可选的，当 client_list 的客户端试图访问从 daemon_list 访问服务器 daemon 程序时指代被执行的命令。




tcpwrraper自身工作在内核，却可以通过这两个文件来提供访问控制

 

                   请求

           ↓              

        服务   --->/etc/hosts.allow     --->     如有有就放行

                                     ↓         没有

                           /etc/hosts.deny     ---->    如果没有就放行     

                                     ↓                    

                                                        如果有明确的匹配条目拒绝

/etc/hosts.allow|deny文件格式：damon_list: client_list [:option]

匹配服务列表：damon_list

vsftpd: 192.168.0.            

vsftpd,sshd,in.telnetd:

ALL                    

daemon@host

vsftpd@192.168.0.186                    

#可以简写192.168.0  代表192.168.0.0网段

#可以一次指定多个服务

#可以使用通配符ALL来指定所有的服务

#@只对某个主机来控制

 

匹配客户端列表clent_list

IP

network address

     network/mask:   mask不能使用长度格式 ，只能是有完全ip的格式

     172.16.0.0--->172.16.    也可以简写网段

HOSTNAME

           fqdn

           .a.rog            表示a.org域内的所有主机    

 

option  选项

spawn                     #可以通过spawn来实现日志定义

spawn echo ""     

#下面定义一个条件，只要用户通过telnet登录就记录到一个日志

vim /etc/hosts.deny

in.telnetd:ALL EXCEPT 172.16.0.1: spawnecho "Login attemp(`date`) %u from %a attemp to login %A, the deamon is%d." >> /var/log/telnet.log

##注意不能在echo后面用: 分号在这里有特殊意义。

##可以通过man 5 hosts_access 来查看上述%的含义

 

常用的宏定义 MACRO

ALL                                   #代表所有主机,或者所有服务

LOCAL                                   #表示本地主机，非FQDN主机

KNOWN                                      #表示可以被解析的主机

UNKNOWN                #反向可以被解析的主机

PARANOID                #正反向解析不匹配的主机

EXCEPT                                    #排除某个主机或某个网络 

 

-

怎么来控制vsftpd的访问

    1. which vsftpd                       # 确定文件路径        
    2. vim /etc/hosts.deny                 # 改文件立即生效
    vsftpd: 172.16.100.100
     
    #假设仅允许172.16网段访问
    vim /etc/hosts.allow
    vsftpd: 171.16                        # 只允许172.16.0.0/16网段
    vim /etc/hosts.deny
    ALL: ALL                                            # 拒绝其他的用户登陆

这样就只有172.16.0.0网段可以访问了

 

 

控制SSH ，只允许192.168.0.32网段登陆，并且不允许192.168.0.32登陆

    vim /etc/hosts.deny
    ALL:ALL
    vim /etc/hosts.allow
    sshd: 192.168.0 EXCEPT 192.168.0.32














在 Linux 系统中有一个特殊的守护进程 inetd（InterNET services Daemon），它用于 Internet 标准服务，通常随系统自动启动。inetd 的主要任务是为那些在系统初始化时没有启动的服务监听请求，当一个请求到达由 inetd 管理的服务端口时，inetd 会将该请求转发给名为  tcpd 的程序。tcpd 根据配置文件判断是否允许请求，如果请求被允许则会启动相应的服务器程序（如：ftpd、 telnet）。

inetd
********************************




tcpd
********************************




















inetd（Internet服务守护程序）是许多提供Internet服务的Unix系统上的超级服务器守护程序。对于每个已配置的服务，它将侦听来自连接客户端的请求。根据请求运行的外部可执行文件可以是单线程或多线程。

通常称为超级服务器，inetd侦听Internet服务（如FTP，POP3和telnet）使用的指定端口。当TCP数据包或UDP数据包到达时具有特定目标端口号时，inetd会启动相应的服务器程序来处理连接。对于预计不会以高负载运行的服务，此方法更有效地使用内存，因为特定服务器仅在需要时运行。此外，特定于服务的程序中不需要网络代码，因为inetd将套接字直接挂钩到生成进程的stdin，stdout和stderr。对于具有频繁流量的协议，例如HTTP和POP3，可以优选直接截取流量的专用服务器。

inetd被称作 “Internet 超级服务器”， 因为它可以为多种服务管理连接。 当 inetd 接到连接时， 它能够确定连接所需的程序， 启动相应的进程，并把 socket 交给它 (服务 socket 会作为程序的标准输入、 输出和错误输出描述符)。 使用 inetd 来运行那些负载不重的服务有助于降低系统负载，因为它不需要为每个服务都启动独立的服务程序。



xinetd（eXended InterNET services Daemon）提供类似于inetd+tcp_wrapper的功能，但是更加强大和安全。在主流的 Linux 发行版中已经逐渐用xinetd取代了inetd，并且提供了访问控制、加强的日志和资源管理功能，成了Linux系统的Internet标准超级守护进程。很多系统服务都用到了xinetd如：FTP、IMAP、POP和telnet等。/etc/services中所有的服务通过他们的端口来访问服务器的时候，先由xinetd来处理，在唤起服务请求之前，xinetd先检验请求者是否满足配置文件中指定的访问控制规则，当前的访问是否超过了指定的同时访问数目，还有配置文件中指定的其他规则等，检查通过，xinetd将这个请求交付到相应的服务去处理，自己就进入sleep状态，等待下一个请求的处理。




Xinetd是Internet服务守护程序inetd的安全替代品。 Xinetd根据远程主机的地址和/或访问时间为所有服务提供访问控制，并可以防止拒绝访问攻击。 Xinetd提供广泛的日志记录，对服务器参数的数量没有限制，并允许用户将特定服务绑定到主机上的特定IP地址。每个服务都有自己的Xinetd特定配置文件;这些文件位于/etc/xinetd.d目录中。






xinetd 提供类似于 inetd + TCP Wrappers 的功能，但是更加强大和安全。后面 xinetd 已经取代了 inetd，并且提供了访问控制、加强的日志和资源管理功能。

.. note::

    注意：xinetd 只是取代了 inetd，想要完成的功能还需要安装 tcp_wrappers 。

TCP Wrappers 是一个应用层的访问控制程序，其原理是在服务器向外提供的 TCP 服务上包裹一层安全检测机制。外来的连接请求首先要通过这层安全检测，获得认证之后才能被系统服务接收。TCP Wrappers 的功能有两种实现方式：一种是由 tcpd 守护进程实现的，常被用于 inetd + TCP Wrappers 的系统中（如FreeBSD等）；另一种是通过每种服务程序调用 libwrap.so 链接库实现的，即 libwrap.so 库支持的网络服务程序都能使用 TCP Wrappers 来实现访问控制，常用于 xinetd + TCP Wrappers 的系统中（如CentOS等）。

在CentOS中，TCP Wrappers 一般是默认安装的，若未安装成功，可以使用如下命令安装：

Linux 守护进程与初始化进程
********************************

服务器的主要任务就是为本地或远程用户提供各种服务。通常 Linux 系统上提供服务的程序是由运行在后台的守护进程（Daemon）来执行。一个实际运行中的 Linux 服务器一般会有多个这样的程序在运行。这些后台守护进程在系统开机后就运行了，并且在时刻地监听前台客户的请求，一旦客户发出了服务请求，守护进程便为它们提供服务。

按照服务类型，守护进程可以分为如下两类：

* 系统守护进程：如 crond（周期任务）、rsyslogd（日志服务）、cpus等；

* 网络守护进程：如 sshd、httpd、xinetd（托管）等。

Linux 操作系统的启动首先从 BIOS 开始，接下来进入 boot loader，由 bootloader 载入内核，进行内核初始化，内核初始化的最后一步就是启动系统初始化进程。系统初始化进程是一个特殊的的守护进程（其 PID 为 1），并且是所有其它守护进程的父进程或者祖先进程。也就是说，系统上所有的守护进程都是由系统初始化进程进行管理的（如启动、停止、重启等）。

在 Linux 的发展过程中，使用过三种 Linux 初始化进程。

1. SysVinit（大名鼎鼎的 init）
   为 UNIX System V 系统创建的；
   RHEL/CentOS 5 及之前的版本一直使用。

2. Upstart
   由 Ubuntu 创建的；
   RHEL/CentOS 6 使用Upstart。

3. Systemd
   先进的初始化系统；
   RHEL/CentOS 7 使用 Systemd（大多数 Linux 发行版都在采用）。

Linux 守护进程运行方式
*******************************

1. 独立运行（stand-alone）的守护进程

独立运行的守护进程由init脚本负责管理，所有独立运行的守护进程的脚本在/etc/rc.d/init.d/目录下。系统服务都是独立运行的守护进程，包括syslogd和cron等。独立运行的守护进程的工作方式称做stand-alone，它是UNIX传统的C/S模式的访问模式。

2．xinetd模式运行独立的守护进程

从守护进程的概念可以看出，对于系统所要通过的每一种服务，都必须运行一个监听某个端口连接所发生的守护进程，这意味着资源浪费。为了解决这个问题，Linux引进了"网络守护进程服务程序"的概念。也就是xinted（extended internet daemon）。xinetd能够同时监听多个指定的端口，在接受用户请求时，它能够根据用户请求的端口的不同，启动不同的网络服务进程来处理这些用户请求。可以把xinetd看做一个管理启动服务的管理服务器，它决定把一个客户请求交给哪个程序处理，然后启动相应的守护进程。xinetd无时不在运行并监听它所管理的所有端口上的服务。当某个要连接它管理的某项服务的请求到达时，xinetd就会为该服务启动合适的服务器。

文件格式
*******************************

先让我们看一个 ``/etc/fstab`` 的例子！

.. highlight:: none

::
