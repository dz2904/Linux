OpenSSH 服务
####################################

SSH 为 Secure Shell 的缩写，简单地说，SSH 是建立在应用层基础上的安全协议。SSH 是目前较可靠，专为远程登录会话和其他网络服务提供安全性的协议。利用 SSH 协议可以有效防止远程管理过程中的信息泄露问题。

OpenSSH 是这种协议的开源实现或实体，OpenSSH 分为客户端 openssh-client 与服务端 openssh-server。在很多 Linux 发行版中，客户端是默认安装的，所以只需要安装服务端，安装完成后默认启动。

.. highlight:: none

::

    apt install openssh-server


配置文件
************************************

OpenSSH 的配置文件在 ``/etc/ssh`` 目录 ，此外还有一些安装时生成的密钥。

- /etc/ssh/sshd_config：主配置文件
- /etc/ssh/ssh_host_ecdsa_key：ECDSA 私钥。
- /etc/ssh/ssh_host_ecdsa_key.pub：ECDSA 公钥。
- /etc/ssh/ssh_host_key：用于 SSH 1 协议版本的 RSA 私钥。
- /etc/ssh/ssh_host_key.pub：用于 SSH 1 协议版本的 RSA 公钥。
- /etc/ssh/ssh_host_rsa_key：用于 SSH 2 协议版本的 RSA 私钥。
- /etc/ssh/ssh_host_rsa_key.pub：用于 SSH 2 协议版本的 RSA 公钥。
- /etc/pam.d/sshd：PAM 配置文件。

.. attention::

    如果重装 sshd，密钥会全部重新生成，导致客户端重新连接服务器时跳出警告，拒绝连接。为了避免这种情况，可以在重装 sshd 时，先备份 ``/etc/ssh`` 目录，重装后再恢复这个目录。

    用户使用 ssh-keygen 生成的密钥文件存放在 ``~/.ssh/`` 目录下，文件权限如下：

    ::

        [Linux]$ ls -l ~/.ssh
        total 12
        -rw------- 1 glenn glenn 1823 Nov 13 10:24 id_rsa
        -rw-r--r-- 1 glenn glenn  400 Nov 13 10:24 id_rsa.pub
        -rw-r--r-- 1 glenn glenn 1106 Nov 13 21:09 known_hosts

        [Linux]$ ls -la ~ | grep ssh
        drwx------  2 glenn glenn  4096 Nov 13 10:28 .ssh


主配置文件：

::

    # 监听的端口号，默认 22 端口
    Port 22
    # 监听地址，默认监听所有
    ListenAddress ::
    ListenAddress 0.0.0.0
    # 支持的 SSH 协议版本号。2 表示仅支持 SSH-2，2,1 表示同时支持 SSH-1 和 SSH-2
    Protocol 2,1
    # HostKey 是主机私钥文件存放的位置
    # 一台主机可以拥有多个不同的私钥。rsa1 仅用于 SSH-1，dsa 和 rsa 仅用于 SSH-2
    HostKey /etc/ssh/ssh_host_rsa_key
    HostKey /etc/ssh/ssh_host_dsa_key
    HostKey /etc/ssh/ssh_host_ecdsa_key
    HostKey /etc/ssh/ssh_host_ed25519_key
    # 创建非特权子进程来处理接入请求，有效防止有缺陷的子进程提升权限，加强系统安全
    UsePrivilegeSeparation yes
    # 在 SSH-1 协议下，服务器密钥重新生成的时间周期。0 表示永不重新生成；默认 3600（秒）
    KeyRegenerationInterval 3600
    # 服务器密钥的位数
    ServerKeyBits 1024
    # 日志消息发送的方式，有效值 DAEMON、USER、AUTH、LOCAL0~7
    SyslogFacility AUTH
    # 日志等级（详细程度）。可用值 QUIET、FATAL、ERROR、INFO、VERBOSE、DEBUG1~3
    LogLevel INFO
    # 用户完成认证的时限。0 表示无限制；默认值是 120（秒）
    LoginGraceTime 120
    # 是否允许 root 登录
    PermitRootLogin yes
    # 是否在接受连接前对用户主目录和相关的配置文件进行权限检查。强烈建议使用默认值 yes
    StrictModes yes
    # 是否允许使用纯 RSA 公钥认证，仅用于 SSH-1
    RSAAuthentication yes
    # 是否允许公钥认证，仅用于 SSH-2
    PubkeyAuthentication yes
    # 是否取消使用 ~/.ssh/.rhosts 来做为认证，推荐设为 yes
    IgnoreRhosts yes
    # 专门用于 version 1 进行认证，推荐设为 no
    RhostsRSAAuthentication no
    # 这个与上面的项目类似，不过用于 version 2
    HostbasedAuthentication no
    # 是否在 RhostsRSA 或 Hostbased 身份验证过程中忽略 ~/.ssh/known_hosts 文件的配置
    IgnoreUserKnownHosts no
    # 是否允许密码为空的用户远程登录
    PermitEmptyPasswords no
    # 是否允许质疑-应答认证。支持所有 login.conf 允许的认证方式
    ChallengeResponseAuthentication no
    # 是否允许使用密码认证
    PasswordAuthentication yes
    # 是否要求用户使用 Kerberos 认证
    KerberosAuthentication no
    # 如果使用了 AFS 且用户有 Kerberos 5 TGT，会在访问用户家目录前尝试获取 AFS  token
    KerberosGetAFSToken no
    # 如果 Kerberos 密码认证失败，那么还将通过其它的认证机制（如 /etc/passwd）
    KerberosOrLocalPasswd yes
    # 是否在用户退出登录后自动销毁用户的 ticket
    KerberosTicketCleanup yes
    # 是否允许使用基于 GSSAPI 的用户认证
    GSSAPIAuthentication no
    # 是否在用户退出登录后自动销毁用户凭证缓存
    GSSAPICleanupCredentials yes
    # 是否允许进行 X11 转发
    X11Forwarding no
    # X11 转发的第一个可用的显示区数字
    X11DisplayOffset 10
    # 是否在每次交互式登录时打印 /etc/motd 文件的内容
    PrintMotd no
    # 是否显示最后一位用户的登录时间
    PrintLastLog yes
    # 是否向客户端发送 TCP keepalive 消息，可用于检测到死连接、客户端崩溃等异常
    TCPKeepAlive yes
    # 是否在交互式会话的登录过程中使用 login
    UseLogin no
    # 未认证的连接同时在线的最多个数。默认值是 10
    MaxStartups 10
    # 每个连接的最多认证次数。默认值是 6
    MaxAuthTries 6
    # 是否对远程主机名进行反向解析，以检查此主机名是否与其 IP 地址真实对应
    UseDNS no
    # 在用户认证时显示的内容。none 表示禁用，仅用于 SSH-2
    Banner /etc/issue.net
    # 配置一个外部子系统（如：一个文件传输守护进程）。仅用于 SSH-2
    Subsystem sftp /usr/lib/openssh/sftp-server
    # 是否使用 PAM 模块认证
    UsePAM yes


应用实例
************************************

密钥登录
====================================

SSH 默认采用密码登录，这种方法有很多缺点，简单的密码不安全，复杂的密码不容易记忆，每次手动输入也很麻烦。密钥登录是比密码登录更好的解决方案。

密钥是一个非常大的数字，通过加密算法得到。SSH 一般使用非对称加密，分为公钥（public key）和私钥（private key）。其中，私钥必须私密保存，不能泄漏；公钥则是公开的，可以对外发送。它们的关系是，公钥和私钥是一一对应的，每一个私钥都有且仅有一个对应的公钥，反之亦然。

SSH 密钥登录分为三步：

1. 客户端通过 :ref:`ssh-keygen 命令 <cmd_ssh-keygen>` 生成公钥和私钥。

::

    # 会询问一系列问题，一路回车即可
    [Linux]$ ssh-keygen -t rsa -b 2048 -C "your_email@domain.com"

2. 将客户端的公钥上传到远程服务器。

将用户公钥文件 ``~/.ssh/id_rsa.pub`` 保存到服务器中，密钥必须保存在服务器用户主目录的 ``~/.ssh/authorized_keys`` 文件中。每个公钥占据一行，如果该文件不存在，可以手动创建。格式为：

::

    [Linux]$ cat ~/.ssh/authorized_keys
    ssh-rsa AAAAB3NzaC1yc2EAAAABIwAAAIEAvpB4lUbAaEbh9u...
    ssh-rsa DydZAKMcDvBJqRhUotQUwqV6HJxqoqPDlPGUUyo8RP...
   
.. attention::

    authorized_keys 文件的权限为 644，即只有文件所有者才能写。如果权限设置不正确，SSH 服务器可能会拒绝读取该文件。

也可以使用命令自动将公钥上传到远程服务器：

::

    [Linux]$ ssh-copy-id -i ~/.ssh/id_rsa.pub  user@host


3. 客户端使用密钥登录服务器。

::

    [Linux]$ ssh user@host

为了安全性，启用密钥登录之后，最好关闭服务器的密码登录。在主配置文件中加入 ``PasswordAuthentication no`` 。


root 登录
====================================

一般情况下默认的配置已经很好，不需要自主配置。但有时需要增加 root 登录的权限，可以直接在文件末尾添加配置：

::

    # vim /etc/ssh/sshd_config
    PermitRootLogin yes

    # 重启 OpenSSH 服务
    [Linux]# systemctl restart sshd
