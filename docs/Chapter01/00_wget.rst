.. _cmd_wget:

wget 下载文件
####################################

wget 是在命令行中下载文件的工具。wget 支持 HTTP，HTTPS 和 FTP 协议，还可使用 HTTP 代理。

wget 可以跟踪 HTML 页面上的链接依次下载来创建远程服务器的本地副本，常被称作“递归下载”。在递归下载的时候，wget 遵循 Robot Exclusion 标准（robots.txt）。wget 非常稳定，在带宽有限和不稳定的网络中有很强的适应性。当下载文件失败时，wget 会不断的尝试继续下载，直到整个文件下载完毕。


命令格式
************************************

.. highlight:: none

::

    wget [option]...  [URL]...


常用选项：
************************************

::

    -b, –background 
        启动后转入后台执行

    -q, –quiet
        安静模式（没有输出）

    -v, –verbose
        冗长模式，显示详细信息（默认设置）

    -c, –continue
        继续下载没下载完的文件

    -N, –timestamping
        不要重新下载文件除非比本地文件新

    -T, –timeout=SECONDS
        设定响应超时的秒数

    -Y, -proxy=on/off 
        打开或关闭代理

    -P, –directory-prefix=PREFIX 
        指定下载的目录

    -U, –user-agent=AGENT 
        设定代理的名称为 AGENT 而不是 Wget/VERSION

    cookies=off 
        不使用 cookies

    passive-ftp 
        FTP 选项，使用被动传输模式（缺省值）

    active-ftp 
        FTP 选项，使用主动传输模式

    -r, –recursive 
        递归下载－－慎用!

    -l, –level=NUMBER
        最大递归深度（inf 或 0 代表无穷）
        
    -A, –accept=LIST
        分号分隔的被接受扩展名的列表


使用实例：
************************************

::

    # 下载单个文件，在下载过程中显示详细信息
    [Linux]$ wget https://wordpress.org/latest.tar.gz

    # 下载文件并重命名，默认以最后一个 / 后面的字符来命名
    [Linux]$ wget -O wordpress.tgz https://wordpress.org/latest.tar.gz

    # 断点续传，继续下载中断的文件
    [Linux]$ wget -c

    # 后台下载
    [Linux]$ wget -b https://wordpress.org/latest.tar.gz
    Continuing in background, pid 1840.
    Output will be written to 'wget-log'.
    [Linux]$ tail wget-log

    # 下载多个文件，需要一个下载链接文件
    [Linux]$ cat filelist.txt
    https://wordpress.org/latest.tar.gz
    https://wordpress.org/latest.zip

    [Linux]$ wget -i filelist.txt


高级用法
====================================

1. 镜像网站（下载整个网站到本地）

::

    [Linux]$ wget -mirror -p --convert-links -page-requisites URL

* -m：镜像下载
* -p：下载所有 html 页面显示正常的文件
* -convert-links：下载后，转换成本地的链接
* -page-requisites：下载额外的东西，如样式表

2. 下载指定格式文件

::

    [Linux]$ wget -r -A .pdf,jpg url

可以在以下情况使用该功能：

* 下载一个网站的所有图片
* 下载一个网站的所有视频
* 下载一个网站的所有PDF文件

3. 下载 FTP 服务器中的文件

::

    # 匿名下载
    [Linux]$ wget ftp-url
    
    # 指定用户名和密码
    [Linux]$ wget --ftp-user=USERNAME --ftp-password=PASSWORD url

