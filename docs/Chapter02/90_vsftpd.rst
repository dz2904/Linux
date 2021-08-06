vsftpd 配置文件
############################

vsftpd（very secure FTP daemon）是一个完全免费、开源的 ftp 服务器软件，支持很多其他的 FTP 服务器所不支持的特征。比如：非常高的安全性需求、带宽限制、良好的可伸缩性、可创建虚拟用户、支持IPv6、速率高等。

在 vsftpd 的诸多文件中，所有的配置都是基于 vsftpd.conf 的。

===============================   ================
文件                                 说明
===============================   ================
/etc/vsftpd.conf                     主配置文件
/usr/sbin/vsftpd                     主程序
/etc/pam.d/vsftpd                     PAM 认证文件
/etc/ftpusers                        禁止使用的用户列表，每行一个用户名
/etc/vsftpd/user_list                禁止或允许使用的用户列表。详见 userlist_deny
/srv/ftp                             匿名用户主目录
/etc/logrotate.d/vsftpd.log          日志文件
===============================   ================


主配置文件
************************************

vsftpd 的主配置文件路径为 ``/etc/vsftpd.conf`` ，在修改配置文件时，注释掉行可以关闭默认配置，修改前请备份。

- anonymous_enable  是否允许匿名登录服务器
- local_enable  是否允许本地用户登录服务器
- write_enable  是否允许本地用户具有写权限
- local_umask  本地用户的文件掩码
- anon_upload_enable  是否允许匿名用户上传文件，须开启 write_enable 选项
- anon_mkdir_write_enable  是否允许匿名用户创建新文件夹
- dirmessage_enable  是否激活目录欢迎信息功能
- xferlog_enable  如果启用此选项，系统将会记录服务器上传和下载的日志文件，默认日志文件为 /var/log/vsftpd.log，也可以通过 xferlog_file 选项设定
- xferlog_file=/var/log/vsftpd.log  服务器上传、下载的日志存储路径
- xferlog_std_format  以 xferlog 格式记录日志文件
- syslog_enable  是否将日志写入系统日志中
- connect_from_port_20=YES  开启主动模式后是否启用默认的 20 端口监听
- chown_uploads  是否允许改变上传文件的属主，与下面选项配合使用
- chown_username  改变上传文件的属主，输入一个系统用户名，whoever：任何人
- idle_session_timeout  数据传输中断间隔时间
- data_connection_timeout  数据连接超时时间
- nopriv_user=ftpsecure  运行 vsftpd 需要的非特权系统用户
- use_localtime 是否使用主机的时间，默认使用 GMT 时间，比北京时间晚 8小时，建议设定为 YES
- ascii_upload_enable  以 ASCII 方式上传数据
- ascii_download_enable 以 ASCII 方式下载数据
- ftpd_banner  登录 FTP 服务器时显示的欢迎信息
- chroot_list_enable  用户是否具有访问自己目录以外文件的权限，设置为 YES 时，用户被锁定在自己的 home 目录中
- chroot_list_file=/etc/vsftpd/chroot_list 不能访问自己目录以外的用户名，需要和 chroot_list_enable 配合使用
- ls_recurse_enable  是否允许递归查询
- listen  是否让 vsftpd 以独立模式运行，由 vsftpd 自己监听和处理连接请求
- listen_ipv6  是否支持 IPV6
- userlist_enable  是否阻止 ftpusers 文件中的用户登录服务器
- userlist_deny  是否阻止 user_list 文件中的用户登录服务器
- tcp_wrappers  是否使用 tcp_wrappers 作为主机访问控制方式
- max_client  允许的最大客户端连接数，0 为不限制
- max_per_ip  同一 IP 允许的最大客户端连接数，0 为不限制
- local_max_rate  本地用户的最大传输速率（单位：B/s），0 为不限制
- anon_max_rate  匿名用户的最大传输速率


.. hint::

    修改完配置文件，需要使用 ``systemclt restart vsftpd`` 命令重启服务才能生效。


应用实例
************************************

1. 匿名用户登录最小配置
++++++++++++++++++++++++++++++++++++

.. highlight:: none

::

   # 不以独立模式运行
   listen=NO
   # 支持 IPV6，如不开启 IPV4 也无法登录
   listen_ipv6=YES
   # 匿名用户登录
   anonymous_enable=YES
   # 系统用户登录
   local_enable=YES
   # 对文件具有写权限，否则无法上传
   write_enable=YES
   # 允许匿名用户上传文件
   anon_upload_enable=YES
   # 允许匿名用户新建文件夹
   anon_mkdir_write_enable=YES
   # 匿名用户删除文件和重命名文件
   anon_other_write_enable=YES
   # 匿名用户的掩码（022 的实际权限为 666-022=644）
   anon_umask=022
   # 匿名用户访问路径
   anon_root=/var/www/html/web
   # 指定端口号
   listen_port=5001
   # 使用主机时间
   use_localtime=YES
   pam_service_name=vsftpd

.. attention ::

    由于 vsftpd 增强了安全检查，如果用户被限定在其主目录下，则用户的主目录不能具有写权限，如果还有写权限，就会报该错误：

    500 OOPS: vsftpd: refusing to run with writable root inside chroot()

    要修复这个错误，可以删除用户的写权限 ``chmod a-w /var/www/html/web`` 。
    或者在 vsftpd 的配置文件中增加下列两项：

    ::

        chroot_local_user=YES 
        allow_writeable_chroot=YES


2. 设置禁止登录的用户账号
++++++++++++++++++++++++++++++++++++

当主配置文件中包括以下设置时，vsftpd.user_list 中的用户账号被禁止登录

::

    userlist_enable=YES
    userlist_deny=YES
    userlist_enable=YES

userlist_deny 和 userlist_enable 选项限制用户登录服务器，可以有效阻止 root,apache,www 等系统用户登录服务器，从而保证服务器的分级安全性。


::

   userlist_enable=YES 
       ftpusers 中用户允许访问，user_list 中用户允许访问

   userlist_enable=NO
       ftpusers 中用户禁止访问，user_list 中用户允许访问

   userlist_deny=YES
       ftpusers 中用户禁止访问，user_list 中用户禁止访问

   userlist_deny=NO                
       ftpusers 中用户禁止访问，user_list 中用户允许访问

   userlist_deny=YES
   userlist_enable=YES
       ftpusers 中用户禁止访问，user_list 中用户禁止访问

   userlist_deny=NO 
   userlist_enable=YES
       ftpusers 中用户禁止访问，user_list 中用户允许访问


3. 修改默认端口
++++++++++++++++++++++++++++++++++++

FTP 服务器默认端口号是 21，出于安全目的，有时需修改默认端口号。自定义端口号范围 5001 至 65535。参见 :doc:`计算机端口详解 <../Chapter03/00_port>` 。 

::

   listen_port=5001


FTP 数字代码的意义
************************************

=====   ======
代码      意义
=====   ======
110       重新启动标记应答
120       服务在多久时间内 ready 
125       数据链路端口开启，准备传送
150       文件状态正常，开启数据连接端口
200       命令执行成功
202       命令执行失败
211       系统状态或是系统求助响应
212       目录的状态
213       文件的状态
214       求助的讯息
215       名称系统类型
220       新的联机服务 ready 
221       服务的控制连接端口关闭，可以注销
225       数据连结开启，但无传输动作
226       关闭数据连接端口，请求的文件操作成功
227       进入 passive mode
230       使用者登入
250       请求的文件操作完成
257       显示目前的路径名称
331       用户名称正确，需要密码
332       登入时需要账号信息
350       请求的操作需要进一部的命令
421       无法提供服务，关闭控制连结
425       无法开启数据链路
426       关闭联机，终止传输
450       请求的操作未执行
451       命令终止：有本地的错误
452       未执行命令：磁盘空间不足
500       格式错误，无法识别命令
501       参数语法错误
502       命令执行失败
503       命令顺序错误
504       命令所接的参数不正确
530       未登入
532       储存文件需要账户登入
550       未执行请求的操作
551       请求的命令终止，类型未知
552       请求的文件终止，储存位溢出
553       未执行请求的的命令，名称不正确
=====   ======
