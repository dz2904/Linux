yum 源配置文件
####################################

yum 源一般存放在 ``/etc/yum.repos.d/`` 目录中，文件已 ``.repo`` 结尾。

Yum 源镜像中心提供了 centosplus, cloud, extras, fasttrack, os, updates 等软件库，最好认的软件库就是 os (系统默认的软件) 与 updates (软件升级版本) 啰！最重要的文件就是那个 “repodata” 的目录！该目录就是分析 RPM 软件后所产生的软件属性相依数据放置处！因此，当你要找软件库所在网址时， 最重要的就是该网址底下一定要有个名为 repodata 的目录存在！那就是软件库的网址了！

.. highlight:: none

::

    [main]
    cachedir=/var/cache/yum
    debuglevel=2
    logfile=/var/log/yum.log
    pkgpolicy=newest
    distroverpkg=redhat-release
    tolerant=1
    exactarch=1
    retries=1

    [base]
    name=Fedora Core $releasever - $basearch - Base
    baseurl=http://download.atrpms.net/mirrors/fedoracore/$releasever/$basearch/os
    http://rpmfind.net/linux/fedora/cor...er/$basearch/os
    http://mirror.clarkson.edu/pub/dist...er/$basearch/os

    [updates-released]
    name=Fedora Core $releasever - $basearch - Released Updates
    baseurl=http://download.atrpms.net/mirrors/fedoracore/updates/$releasever/$basearch
    http://redhat.linux.ee/pub/fedora/l...sever/$basearch
    http://fr2.rpmfind.net/linux/fedora...sever/$basearch

    ... 中间省略 ...

    [freshrpms]
    name=FreshRPMs
    baseurl=http://ayo.freshrpms.net/fedora/linux/$releasever/$basearch/freshrpms/
    http://ftp.us2.freshrpms.net/linux/...arch/freshrpms/



第一部分(全局性设置，一般不必改动。)
************************************

**[main]**

**cachedir：** yum 缓存的目录，存储下载的 rpm 包和数据库，一般是 ``/var/cache/yum`` 。

**debuglevel：** 除错级别，0-10，默认是2。

**logfile：** yum 的日志文件，默认是 ``/var/log/yum.log`` 。

**pkgpolicy：** 选择安装软件包的策略。一共有两个选项，newest和last（如果你设置了多个 repository，同一软件在不同的 repository 中同时存在。yum 应该安装哪一个，如果是 newest，则 yum 会安装最新的那个版本。如果是 last，则 yum 会将服务器 id 以字母表排序，并选择最后的那个服务器上的软件安装。默认为 newest。

**distroverpkg：** 指定一个软件包，yum 会根据这个包判断你的发行版本，默认是 redhat-release，也可以是安装的任何针对自己发行版的 rpm 包。

**exactarch：** 有两个选项1和0（1代表只升级和你安装软件包 cpu 体系一致的包，例如：你安装了一个i386的 rpm，则 yum 不会用1686的包来升级这个包）。

**retries：** 网络连接发生错误后的重试次数（如果设为0，则会无限重试）。

**tolerent：** 有1和0两个选项，表示 yum 是否提示与软件包有关的错误，（1代表 yum 不会提示错误信息，默认是0）。

除了上述之外，还有一些可以添加的选项，如

exclude=，排除某些软件在升级名单之外，可以用通配符，列表中各个项目要用空格隔开，这个对于安装了诸如美化包，中文补丁的朋友特别有用。

gpgchkeck= 有 1 和 0 两个选择，分别代表是否是否进行gpg校验，如果没有这一项，默认好像也是检查的。


第二部分（个性化配置）
************************************

配置 repository 服务器了，这是最令人激动的。有了好的 repository，就如家门口开了大卖场，要什么东西稍微跑跑腿就行，对了这还是个免费的大卖场。所有服务器设置都应该遵循如下格式：

**[base]：** 代表软件库的名字！中括号一定要存在，里面的名称则可以随意取。但是不能有两个相同的软件库名称， 否则 yum 会不晓得该到哪里去找软件库相关软件列表档案。

**name：** 只是说明一下这个软件库的意义而已，重要性不高！

**mirrorlist=：** 列出这个软件库可以使用的映射站台，如果不想使用，可以注释掉这行；

**baseurl=：** 这个最重要，因为后面接的就是软件库的实际网址！ mirrorlist 是由 yum 程序自行去捉映像站台， baseurl 则是指定固定的一个软件库网址！我们刚刚找到的网址放到这里来啦！

**enable=1：** 就是让这个软件库被启动。如果不想启动可以使用 enable=0 喔！

**gpgcheck=1：** 还记得 RPM 的数字签名吗？这就是指定是否需要查阅 RPM 档案内的数字签名！

**gpgkey=：** 就是数字签名的公钥文件所在位置！使用默认值即可


几个变量
====================================

$releasever，发行版的版本，从[main]部分的distroverpkg获取，如果没有，则根据redhat-release包进行判断。

$arch，cpu体系，如i686,athlon等

$basearch，cpu的基本体系组，如i686和athlon同属i386，alpha和alphaev6同属alpha。



CentOS下安装yum
************************************

在Linux里面依次输入下面的命令：

1. 下载最新的 yum-3.2.28.tar.gz 并解压

::

    wget http://yum.baseurl.org/download/3.2/yum-3.2.28.tar.gz

    tar xvf yum-3.2.28.tar.gz  

2. 进入目录，运行安装

::

    cd yum-3.2.28/  
    ./yummain.py install yum  

如果结果提示错误： CRITICAL:yum.cli:Config Error: Error accessing file for config file:///etc/

可能是原来是缺少配置文件。在 etc 目录下面新建 yum.conf 文件，然后再次运行 yummain.py install yum，顺利完成安装。

3，最后更新系统。

::

    yum check-update  
    yum update  
    yum clean all  
