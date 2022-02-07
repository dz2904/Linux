.. _cmd_scp:

scp 远程复制文件
####################################

scp 是 secure copy 的简写，用于在本机和服务器或两台服务器之间远程复制文件，而且 scp 传输是加密的。另外，scp 还非常节省资源，不会占用太多系统负荷。


命令格式：
************************************

.. highlight:: none

::

    scp [-346ABCpqrTv] [-c cipher] [-F ssh_config] [-i identity_file]
        [-J destination] [-l limit] [-o ssh_option] [-P port]
        [-S program] source ... target



常用选项：
************************************

::

    -4
      只使用IPv4寻址

    -6
      只使用IPv6寻址

    -p
      保留原文件的修改时间，访问时间和访问权限。

    -r
      递归复制整个目录。

    -v
      详细显示过程信息。

    -P
      指定数据传输的端口号。


使用实例：
***********************

::

    # 复制（上传）本机文件到服务器
    [Linux]$ scp ~/Music/1.mp3 pi@www.pi.com:/home/pi/Music
    1.mp3                       100% 2674     8.4KB/s   00:00

    # 复制（上传）本机文件到服务器并重命名
    [Linux]$ scp ~/Music/1.mp3 pi@www.pi.com:/home/pi/Music/a.mp3
    1.mp3                       100% 2674     8.4KB/s   00:00

    # 复制（上传）本机文件夹到服务器并重命名
    [Linux]$ scp -r ~/Music/ pi@www.pi.com:/home/pi/
    1.mp3                       100% 2674     8.4KB/s   00:00
    2.mp3                       100%  523    81.0KB/s   00:00
    3.mp3                       100% 3463   119.9KB/s   00:00

    # 复制（下传）服务器文件到本机
    [Linux]$ scp pi@www.pi.com:/home/pi/Music/1.mp3 ~/Music
    1.mp3                       100% 2674     8.4KB/s   00:00


    # 复制（下传）服务器文件到本机
    # 如果服务器防火墙为 scp 设置了端口号，传输时需要指定端口号
    [Linux]$ scp -P 12345 pi@www.pi.com:/home/pi/Music/1.mp3 ~/Music
    1.mp3                       100% 2674     8.4KB/s   00:00
