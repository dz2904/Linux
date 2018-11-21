bash 别名
##########################

bash 别名只是指向命令的快捷方式而已（就像是软链接）。alias 命令允许用户只输入一个单词就运行任意一个命令或一组命令（包括命令选项和文件名）。执行 alias 命令会显示所有已定义别名的列表，你可以在 ``~/.bashrc`` 文件中自定义别名。使用别名可以在命令行中减少输入的时间，使工作更流畅，同时增加生产率。

列出别名
***********************

alias 命令默认会列出当前用户定义好的别名：

.. highlight:: none

::

    [shiyanlou:/home/]$ alias
    -='cd -'
    ...=../..
    ....=../../..
    .....=../../../..
    ......=../../../../..
    1='cd -'
    2='cd -2'
    3='cd -3'
    4='cd -4'
    5='cd -5'
    6='cd -6'
    7='cd -7'
    8='cd -8'
    9='cd -9'
    _=sudo
    afind='ack -il'
    d='dirs -v | head -10'
    g=git
    ga='git add'
    gaa='git add --all'
    gapa='git add --patch'
    gb='git branch'
    gba='git branch -a'



新建一个别名
**************************

使用下面语法新建别名，需要注意一点，如果新建的别名名称已经使用了，那么就会更新为最新的设置::


    alias name = value
    alias name = 'command'
    alias name = 'command arg1 arg2'
    alias name = '/path/to/script'
    alias name = '/path/to/script.pl arg1'


举个例子，为常用的 clear（清除屏幕）命令创建一个别名 c::

    [shiyanlou:/home/]$ alias c = 'clear'


临时性地禁用 bash 别名
**************************

以下的方法都可以临时性地禁用 ls 别名::

    在命令前边加入转义符：
    [shiyanlou:/home/]$ \ls
    
    使用命令的绝对路径：
    [shiyanlou:/home/]$ /usr/bin/ls
    
    或者下边的一种：
    [shiyanlou:/home/]$ "ls"
    [shiyanlou:/home/]$ 'ls'
    [shiyanlou:/home/]$ command ls

删除别名
**************************

使用 unalias 命令来删除别名。其语法为::

    unalias aliasname
    unalias foo


例如，删除我们之前创建的别名 c::

    [shiyanlou:/home/]$ unalias c


让别名永久生效
**************************

在设置别名后，只在当前登录会话中有效。如果你登出或重启系统后，别名就会消失了。你可以将别名定义写入配置文件 ``~/.bashrc`` 中，输入::

  [shiyanlou:/home/]$ vi ~/.bashrc


输入下行内容让别名 c 对当前用户永久有效::

  alias c = 'clear'


保存并关闭文件就行了。系统级的别名（也就是对所有用户都生效的别名）可以放在 ``/etc/bashrc`` 文件中。请注意，``alias`` 命令内建于各种 shell 中，包括 ksh，tcsh/csh，ash，bash 以及其他 shell。

关于权限判断
**************************

可以将下面代码加入 ``~/.bashrc`` ::

    # if user is not root, pass all commands via sudo #
    if [ $UID -ne 0 ]; then
        alias reboot='sudo reboot'
        alias update='sudo apt-get upgrade'
    fi


30 个 bash shell 别名的案例
***********************************

你可以定义各种类型的别名来节省时间并提高生产率。

1. 控制 ls 命令的输出（对输出内容进行着色）::

    ## Colorize the ls output ##
    alias ls = 'ls --color=auto'
    ## Use a long listing format ##
    alias ll = 'ls -la'
    ## Show hidden files ##
    alias l.= 'ls -d . .. .git .gitignore .gitmodules .travis.yml --color=auto'


2. 控制 cd 命令的行为::

    ## Colorize the ls output ##
    alias ls = 'ls --color=auto'
    ## Use a long listing format ##
    alias ll = 'ls -la'
    ## Show hidden files ##
    alias l.= 'ls -d . .. .git .gitignore .gitmodules .travis.yml --color=auto'


3. 控制 grep 命令的输出，grep 命令是一个用于在纯文本文件中搜索匹配正则表达式的行的命令行工具::

    ## Colorize the grep command output for ease of use (good for log files)##
    alias grep = 'grep --color=auto'
    alias egrep = 'egrep --color=auto'
    alias fgrep = 'fgrep --color=auto'


4. 让计算器默认开启 math 库::

    alias bc = 'bc -l'


5. 生成 sha1 数字签名::

    alias sha1 = 'openssl sha1'


6. 自动创建父目录，mkdir 命令用于创建目录::

    alias mkdir = 'mkdir -pv'


7. 为 diff 输出着色，你可以使用 diff 来一行行第比较文件，而一个名为 colordiff 的工具可以为 diff 输出着色::

    # install colordiff package :)
    alias diff = 'colordiff'


#. 让 mount 命令的输出更漂亮，更方便人类阅读::

    alias mount = 'mount |column -t'


#. 简化命令以节省时间::

    # handy short cuts #
    alias h = 'history'
    alias j = 'jobs -l'


#. 创建一系列新命令::

    alias path = 'echo -e ${PATH//:/\\n}'
    alias now = 'date +"%T"'
    alias nowtime =now
    alias nowdate = 'date +"%d-%m-%Y"'


#. 设置 vim 为默认编辑器::

    alias vi = vim
    alias svi = 'sudo vi'
    alias vis = 'vim "+set si"'
    alias edit = 'vim'


#. 控制网络工具 ping 的输出::

    # Stop after sending count ECHO_REQUEST packets #
    alias ping = 'ping -c 5'
    # Do not wait interval 1 second, go fast #
    alias fastping = 'ping -c 100 -i.2'


#. 显示打开的端口，使用 netstat 命令，可以快速列出服务区中所有的 TCP/UDP 端口::

    alias ports = 'netstat -tulanp'


#. 唤醒休眠的服务器，Wake-on-LAN (WOL) 是一个以太网标准，可以通过网络消息来开启服务器。你可以使用下面别名来快速激活 nas 设备 以及服务器::

    ## replace mac with your actual server mac address #
    alias wakeupnas01 = '/usr/bin/wakeonlan 00:11:32:11:15:FC'
    alias wakeupnas02 = '/usr/bin/wakeonlan 00:11:32:11:15:FD'
    alias wakeupnas03 = '/usr/bin/wakeonlan 00:11:32:11:15:FE'


#. 控制防火墙 (iptables) 的输出，Netfilter 是一款 Linux 操作系统上的主机防火墙。它是 Linux 发行版中的一部分，且默认情况下是激活状态。这里列出了大多数 Liux 新手防护入侵者最常用的 iptables 方法::

    ## shortcut for iptables and pass it via sudo#
    alias ipt = 'sudo /sbin/iptables'
    # display all rules #
    alias iptlist = 'sudo /sbin/iptables -L -n -v --line-numbers'
    alias iptlistin = 'sudo /sbin/iptables -L INPUT -n -v --line-numbers'
    alias iptlistout = 'sudo /sbin/iptables -L OUTPUT -n -v --line-numbers'
    alias iptlistfw = 'sudo /sbin/iptables -L FORWARD -n -v --line-numbers'
    alias firewall =iptlist


#. 使用 curl 调试 web 服务器 / CDN 上的问题::

    # get web server headers #
    alias header = 'curl -I'
    # find out if remote server supports gzip / mod_deflate or not #
    alias headerc = 'curl -I --compress'


#. 增加安全性::

    # do not delete / or prompt if deleting more than 3 files at a time #
    alias rm = 'rm -I --preserve-root'
    # confirmation #
    alias mv = 'mv -i'
    alias cp = 'cp -i'
    alias ln = 'ln -i'
    # Parenting changing perms on / #
    alias chown = 'chown --preserve-root'
    alias chmod = 'chmod --preserve-root'
    alias chgrp = 'chgrp --preserve-root'


#. 更新 Debian Linux 服务器，apt-get 命令 用于通过因特网安装软件包 (ftp 或 http)。你也可以一次性升级所有软件包::

    # distro specific - Debian / Ubuntu and friends #
    # install with apt-get
    alias apt-get= "sudo apt-get"
    alias updatey = "sudo apt-get --yes"
    # update on one command
    alias update = 'sudo apt-get update && sudo apt-get upgrade'


#. 更新 RHEL / CentOS / Fedora Linux 服务器::

    ## distrp specifc RHEL/CentOS ##
    alias update = 'yum update'
    alias updatey = 'yum -y update'


#. 优化 sudo 和 su 命令::

    # become root #
    alias root = 'sudo -i'
    alias su = 'sudo -i'


#. 使用 sudo 执行 halt/reboot 命令，shutdown 命令会让 Linux / Unix 系统关机::

    # reboot / halt / poweroff
    alias reboot = 'sudo /sbin/reboot'
    alias poweroff = 'sudo /sbin/poweroff'
    alias halt = 'sudo /sbin/halt'
    alias shutdown = 'sudo /sbin/shutdown'


#. 控制 web 服务器::

    # also pass it via sudo so whoever is admin can reload it without calling you #
    alias nginxreload = 'sudo /usr/local/nginx/sbin/nginx -s reload'
    alias nginxtest = 'sudo /usr/local/nginx/sbin/nginx -t'
    alias lightyload = 'sudo /etc/init.d/lighttpd reload'    
    alias lightytest = 'sudo /usr/sbin/lighttpd -f /etc/lighttpd/lighttpd.conf -t'
    alias httpdreload = 'sudo /usr/sbin/apachectl -k graceful'
    alias httpdtest = 'sudo /usr/sbin/apachectl -t && /usr/sbin/apachectl -t -D DUMP_VHOSTS'


#. 与备份相关的别名::

    # if cron fails or if you want backup on demand just run these commands #
    # again pass it via sudo so whoever is in admin group can start the job #
    # Backup scripts #
    alias backup = 'sudo /home/scripts/admin/scripts/backup/wrapper.backup.sh --type local --taget /raid1/backups'
    alias nasbackup = 'sudo /home/scripts/admin/scripts/backup/wrapper.backup.sh --type nas --target nas01'
    alias s3backup = 'sudo /home/scripts/admin/scripts/backup/wrapper.backup.sh --type nas --target nas01 --auth /home/scripts/admin/.authdata/amazon.keys'
    alias rsnapshothourly = 'sudo /home/scripts/admin/scripts/backup/wrapper.rsnapshot.sh --type remote --target nas03 --auth /home/scripts/admin/.authdata/ssh.keys --config /home/scripts/admin/scripts/backup/config/adsl.conf'
    alias rsnapshotdaily = 'sudo /home/scripts/admin/scripts/backup/wrapper.rsnapshot.sh --type remote --target nas03 --auth /home/scripts/admin/.authdata/ssh.keys --config /home/scripts/admin/scripts/backup/config/adsl.conf'
    alias rsnapshotweekly = 'sudo /home/scripts/admin/scripts/backup/wrapper.rsnapshot.sh --type remote --target nas03 --auth /home/scripts/admin/.authdata/ssh.keys --config /home/scripts/admin/scripts/backup/config/adsl.conf'
    alias rsnapshotmonthly = 'sudo /home/scripts/admin/scripts/backup/wrapper.rsnapshot.sh --type remote --target nas03 --auth /home/scripts/admin/.authdata/ssh.keys --config /home/scripts/admin/scripts/backup/config/adsl.conf'
    alias amazonbackup =s3backup


#. 桌面应用相关的别名 - 按需播放的 avi/mp3 文件::

    ## play video files in a current directory ##
    # cd ~/Download/movie-name
    # playavi or vlc
    alias playavi = 'mplayer *.avi'
    alias vlc = 'vlc *.avi'
    # play all music files from the current directory #    
    alias playwave = 'for i in *.wav; do mplayer "$i"; done'
    alias playogg = 'for i in *.ogg; do mplayer "$i"; done'
    alias playmp3 = 'for i in *.mp3; do mplayer "$i"; done'
    # play files from nas devices #
    alias nplaywave = 'for i in /nas/multimedia/wave/*.wav; do mplayer "$i"; done'
    alias nplayogg = 'for i in /nas/multimedia/ogg/*.ogg; do mplayer "$i"; done'
    alias nplaymp3 = 'for i in /nas/multimedia/mp3/*.mp3; do mplayer "$i"; done'
    # shuffle mp3/ogg etc by default #
    alias music = 'mplayer --shuffle *'


#. 设置系统管理相关命令的默认网卡，vnstat 一款基于终端的网络流量检测器。dnstop 是一款分析 DNS 流量的终端工具。tcptrack 和 iftop 命令显示 TCP/UDP 连接方面的信息，它监控网卡并显示其消耗的带宽::

    ## All of our servers eth1 is connected to the Internets via vlan / router etc ##
    alias dnstop = 'dnstop -l 5 eth1'
    alias vnstat = 'vnstat -i eth1'
    alias iftop = 'iftop -i eth1'
    alias tcpdump = 'tcpdump -i eth1'
    alias ethtool = 'ethtool eth1'
    # work on wlan0 by default #
    # Only useful for laptop as all servers are without wireless interface
    alias iwconfig = 'iwconfig wlan0'


#. 快速获取系统内存，cpu 使用，和 gpu 内存相关信息::

    ## pass options to free ##
    alias meminfo = 'free -m -l -t'
    ## get top process eating memory
    alias psmem = 'ps auxf | sort -nr -k 4'
    alias psmem10 = 'ps auxf | sort -nr -k 4 | head -10'
    ## get top process eating cpu ##
    alias pscpu = 'ps auxf | sort -nr -k 3'
    alias pscpu10 = 'ps auxf | sort -nr -k 3 | head -10'
    ## Get server cpu info ##
    alias cpuinfo = 'lscpu'    
    ## older system use /proc/cpuinfo ##
    ##alias cpuinfo='less /proc/cpuinfo' ##
    ## get GPU ram on desktop / laptop##
    alias gpumeminfo = 'grep -i --color memory /var/log/Xorg.0.log'


#. 控制家用路由器，curl 命令可以用来 重启 Linksys 路由器::

    # Reboot my home Linksys WAG160N / WAG54 / WAG320 / WAG120N Router / Gateway from *nix.
    alias rebootlinksys = "curl -u 'admin:my-super-password' 'http://192.168.1.2/setup.cgi?todo=reboot'"
    # Reboot tomato based Asus NT16 wireless bridge
    alias reboottomato = "ssh admin@192.168.1.1 /sbin/reboot"


#. wget 默认断点续传，GNU wget 是一款用来从 web 下载文件的自由软件。它支持 HTTP，HTTPS，以及 FTP 协议，而且它也支持断点续传::

    ## this one saved by butt so many times ##
    alias wget = 'wget -c'


#. 使用不同浏览器来测试网站::

    ## this one saved by butt so many times ##
    alias ff4 = '/opt/firefox4/firefox'
    alias ff13 = '/opt/firefox13/firefox'    
    alias chrome = '/opt/google/chrome/chrome'
    alias opera = '/opt/opera/opera'
    #default ff
    alias ff =ff13
    #my default browser
    alias browser =chrome


#. 关于 ssh 别名的注意事项，不要创建 ssh 别名，代之以 ~/.ssh/config 这个 OpenSSH SSH 客户端配置文件。它的选项更加丰富。下面是一个例子::

    Host server10
      Hostname 1.2.3.4
      IdentityFile ~/backups/.ssh/id_dsa
      user foobar
      Port 30000
      ForwardX11Trusted yes
      TCPKeepAlive yes

然后你就可以使用下面语句连接 server10 了::

    $ ssh server10


#. 现在该分享你的别名了::

    ## set some other defaults ##
    alias df = 'df -H'
    alias du = 'du -ch'
    # top is atop, just like vi is vim
    alias top = 'atop'
    ## nfsrestart - must be root ##
    ## refresh nfs mount / cache etc for Apache ##
    alias nfsrestart = 'sync && sleep 2 && /etc/init.d/httpd stop && umount netapp2:/exports/http && sleep 2 && mount -o rw,sync,rsize=32768,wsize=32768,intr,hard,proto=tcp,fsc natapp2:/exports /http/var/www/html && /etc/init.d/httpd start'
    ## Memcached server status ##
    alias mcdstats = '/usr/bin/memcached-tool 10.10.27.11:11211 stats'
    alias mcdshow = '/usr/bin/memcached-tool 10.10.27.11:11211 display'
    ## quickly flush out memcached server ##
    alias flushmcd = 'echo "flush_all" | nc 10.10.27.11 11211'
    ## Remove assets quickly from Akamai / Amazon cdn ##    
    alias cdndel = '/home/scripts/admin/cdn/purge_cdn_cache --profile akamai'
    alias amzcdndel = '/home/scripts/admin/cdn/purge_cdn_cache --profile amazon'
    ## supply list of urls via file or stdin
    alias cdnmdel = '/home/scripts/admin/cdn/purge_cdn_cache --profile akamai --stdin'
    alias amzcdnmdel = '/home/scripts/admin/cdn/purge_cdn_cache --profile amazon --stdin'


总结
**************************

本文总结了 bash 别名的多种用法：

1. 为命令设置默认的参数（例如通过 alias ethtool='ethtool eth0' 设置 ethtool 命令的默认参数为 eth0）。
2. 修正错误的拼写（通过 alias cd..='cd ..'让 cd.. 变成 cd ..）。
3. 缩减输入。
4. 设置系统中多版本命令的默认路径（例如 GNU/grep 位于 /usr/local/bin/grep 中而 Unix grep 位于 /bin/grep 中。若想默认使用 GNU grep 则设置别名 grep='/usr/local/bin/grep' )。
5. 通过默认开启命令（例如 rm，mv 等其他命令）的交互参数来增加 Unix 的安全性。
6. 为老旧的操作系统（比如 MS-DOS 或者其他类似 Unix 的操作系统）创建命令以增加兼容性（比如 alias del=rm）。
