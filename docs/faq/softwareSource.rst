将 ISO 镜像设置为本地源
############################

软件源就是一个应用程序的安装库，很多很多的应用软件都在这个库里面。它可以是网络服务器、光盘，甚至是硬盘上的一个目录。通常大家都会设置网络源，但在一些特殊情况下（如没有网卡驱动），需要从本地安装软件或驱动，这时将 ISO 文件设置为软件源就很实用。

Debian
****************************

以下操作需要 root权限。

1. 挂载 iso 文件:

.. highlight:: none

::

    mount -t iso9660 -o loop /mnt/debian-x.x.x-amd64-DVD-1.iso /media/cdrom/
    注意：挂载到的目录，和步骤 3 使用的是同一个目录。


2. 备份 /etc/apt/sources.list，并清空（注释）文件内容。

3. 添加本地源:

::

    apt-cdrom -m -d /media/cdrom/ add

4. 更新一下:

::

    apt-get update

Centos（待测试）
****************************

操作系统：CentOS 5.5 ，需要 root 权限。

1. 挂载 iso 文件到挂载点：

::

    mount  -o loop /mnt/iso/CentOS5.iso /mnt/cdrom

2. 备份并修改 yum 的配置文件：

::

    # 备份文件
    cd /etc/yum.repos.d/
    cp CentOS-Media.repo CentOS-Media.repo.bak

    # 修改文件
    vi CentOS-Media.repo

    [c5-media]

    name=CentOS-$releasever - Media

    baseurl=file:///mnt/    # 表明 yum 源在 /mnt 目录下，其它的源注释掉

    # baseurl=file:///media/CentOS/

    # file:///media/cdrom/

    # file:///media/cdrecorder/

    gpgcheck=1

    enabled=1               # 启用 yum

    gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-5

3. 清除缓存

::

    yum clean all
    yum list
