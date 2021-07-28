开箱即用的 Shell 脚本
####################################


脚本实例
************************************

检测两台服务器目录的一致性
+++++++++++++++++++++++++++++++++++

通过对比文件或目录的 md5 值，来检测两台服务器指定目录下的文件是否一致。

.. highlight:: none

::

    #!/bin/bash
    ######################################
    检测两台服务器指定目录下的文件一致性
    #####################################
    # 通过对比两台服务器上文件的 md5 值，达到检测一致性的目的
    dir=/data/web
    b_ip=192.168.88.10
    # 将指定目录下的文件遍历出来并作为 md5sum 命令的参数
    # 进而得到所有文件的 md5 值，并写入到指定文件中
    find $dir -type f|xargs md5sum > /tmp/md5_a.txt
    ssh $b_ip "find $dir -type f|xargs md5sum > /tmp/md5_b.txt"
    scp $b_ip:/tmp/md5_b.txt /tmp
    #将文件名作为遍历对象进行一一比对
    for f in `awk '{print 2} /tmp/md5_a.txt'`do
    #以 a 机器为标准，当 b 机器不存在遍历对象中的文件时直接输出不存在的结果
    if grep -qw "$f" /tmp/md5_b.txt
    then
    md5_a=`grep -w "$f" /tmp/md5_a.txt|awk '{print 1}'`
    md5_b=`grep -w "$f" /tmp/md5_b.txt|awk '{print 1}'`
    #当文件存在时，如果 md5 值不一致则输出文件改变的结果
    if [ $md5_a != $md5_b ]then
    echo "$f changed."
    fi
    else
    echo "$f deleted."
    fi
    done


定时清空文件
+++++++++++++++++++++++++++++++++++

通过计划任务使脚本每小时执行一次，当时间为 0 点或 12 点时，将目标目录下的所有文件内容清空，但不删除文件，其他时间则只统计各个文件的大小，一个文件一行，输出到以时间和日期命名的文件中，需要考虑目标目录下二级、三级等子目录的文件

::

    #!/bin/bash
    #################################################################
    定时清空文件，记录文件大小
    ################################################################
    logfile=/tmp/`date +%H-%F`.log
    n=`date +%H`
    if [ $n -eq 00 ] || [ $n -eq 12 ]
    then
    # 通过 for 循环遍历目标目录下的所有文件并做相应操作
    for i in `find /data/log/ -type f`
    do
    true > $i
    done
    else
    for i in `find /data/log/ -type f`
    do
    du -sh $i >> $logfile
    done
    fi

检测网卡流量
+++++++++++++++++++++++++++++++++++

检测网卡流量，并且每分钟记录一次。

::

    #!/bin/bash
    #######################################################
    #检测网卡流量，并按规定格式记录在日志中规定一分钟记录一次
    #日志格式如下所示:
    #2019-08-12 20:40
    #ens33 input: 1234bps
    #ens33 output: 1235bps
    ######################################################3
    while :
    do
    # 设置语言为英文，保障输出结果是英文，否则会出现 bug
    LANG=en
    logfile=/tmp/`date +%d`.log
    # 将下面执行的命令结果输出重定向到 logfile 日志中
    exec >> $logfile
    date +"%F %H:%M"
    # sar 命令统计的流量单位为 kb/s ，日志格式为 bps，因此要 *1000*8
    sar -n DEV 1 59|grep Average|grep ens33|awk '{print $2,"\t","input:","\t",$5*1000*8,"bps","\n",$2,"\t","output:","\t",$6*1000*8,"bps"}'
    echo "####################"
    # 因为执行 sar 命令需要 59 秒，因此不需要 sleep
    done


计算文档每行的数字个数
+++++++++++++++++++++++++++++++++++

计算文档每行出现的数字个数，并计算整个文档的数字总数

::

    #!/bin/bash
    #########################################################
    #计算文档每行出现的数字个数，并计算整个文档的数字总数
    ########################################################
    # 使用 awk 只输出文档行数（截取第一段）
    n=`wc -l a.txt|awk '{print $1}'`
    sum=0
    # 文档中每一行可能存在空格，因此不能直接用文档内容进行遍历
    for i in `seq 1 $n`do
    # 输出的行用变量表示时，需要用双引号
    line=`sed -n "$i"p a.txt`#wc -L选项，统计最长行的长度
    n_n=`echo $line|sed s'/[^0-9]//'g|wc -L`
    echo $n_nsum=$[$sum+$n_n]
    done
    echo "sum:$sum"


统计 .html 文件总大
+++++++++++++++++++++++++++++++++++

统计目录中以 .html 结尾的文件的总大小。

::

    # 方法 1：
    find . -name "*.html" -exec du -k {} \; |awk '{sum+=$1}END{print sum}'

    # 方法 2：
    for size in $(ls -l *.html |awk '{print $5}'); do
        sum=$(($sum+$size))
    done
    echo $sum


扫描主机端口状态
+++++++++++++++++++++++++++++++++++

扫描本地主机的端口状态

::

    #!/bin/bash
    #########################################################
    # 扫描本地主机的端口状态
    ########################################################
    HOST=$1
    PORT="22 25 80 8080"
    for PORT in $PORT; do
        if echo &>/dev/null > /dev/tcp/$HOST/$PORT; then
            echo "$PORT open"
        else
            echo "$PORT close"
        fi
    done


批量修改服务器用户密码
++++++++++++++++++++++++++++++++

Linux 主机 SSH 连接信息：旧密码

::

    # 查看旧密码文件，内容格式：IP User Password Port
    # cat old_pass.txt 
    192.168.18.217  root    123456     22
    192.168.18.218  root    123456     22

    SSH远程修改密码脚本：新密码随机生成
    #!/bin/bash
    OLD_INFO=old_pass.txt
    NEW_INFO=new_pass.txt
    for IP in $(awk '/^[^#]/{print $1}' $OLD_INFO); do
        USER=$(awk -v I=$IP 'I==$1{print $2}' $OLD_INFO)
        PASS=$(awk -v I=$IP 'I==$1{print $3}' $OLD_INFO)
        PORT=$(awk -v I=$IP 'I==$1{print $4}' $OLD_INFO)
        # 随机密码
        NEW_PASS=$(mkpasswd -l 8)  
        echo "$IP   $USER   $NEW_PASS   $PORT" >> $NEW_INFO
        expect -c "
        spawn ssh -p$PORT $USER@$IP
        set timeout 2
        expect {
            \"(yes/no)\" {send \"yes\r\";exp_continue}
            \"password:\" {send \"$PASS\r\";exp_continue}
            \"$USER@*\" {send \"echo \'$NEW_PASS\' |passwd --stdin $USER\r exit\r\";exp_continue}
        }"
    done

    # 生成新密码文件
    # cat new_pass.txt 
    192.168.18.217  root    n8wX3mU%      22
    192.168.18.218  root    c87;ZnnL      22


根据 web 日志，封禁异常 IP
++++++++++++++++++++++++++++++++
    
根据 web 访问日志，封禁请求量异常的 IP，如 IP 在半小时后恢复正常，则解除封禁。

::

    #!/bin/bash
    #####################################################################
    #根据 web 日志，封禁异常 IP，如 IP 在半小时后恢复正常，则解除封禁
    #####################################################################
    logfile=/data/log/access.log
    #显示一分钟前的小时和分钟
    d1=`date -d "-1 minute" +%H%M`
    d2=`date +%M`
    ipt=/sbin/iptables
    ips=/tmp/ips.txt
    block()
    { 
    #将一分钟前的日志全部过滤出来并提取IP以及统计访问次数
     grep '$d1:' $logfile|awk '{print $1}'|sort -n|uniq -c|sort -n > $ips
     #利用for循环将次数超过100的IP依次遍历出来并予以封禁
     for i in `awk '$1>100 {print $2}' $ips` 
     do
     $ipt -I INPUT -p tcp --dport 80 -s $i -j REJECT 
     echo "`date +%F-%T` $i" >> /tmp/badip.log 
     done
    }
    unblock()
    { 
    #将封禁后所产生的pkts数量小于10的IP依次遍历予以解封
     for a in `$ipt -nvL INPUT --line-numbers |grep '0.0.0.0/0'|awk '$2<10 {print $1}'|sort -nr` 
     do 
     $ipt -D INPUT $a
     done
     $ipt -Z
    }
    #当时间在00分以及30分时执行解封函数
    if [ $d2 -eq "00" ] || [ $d2 -eq "30" ] 
     then
     #要先解再封，因为刚刚封禁时产生的pkts数量很少
     unblock
     block 
     else
     block
    fi

