权限
####################################

在 Linux 系统中，我们所有的操作实质都是在进行进程访问文件的操作。我们访问文件需要先取得相应的访问权限，而访问权限是通过 Linux 系统中的安全模型获得的。

对于 Linux 系统中的安全模型，我们需要知道下面两点：

1. Linux 系统上最初的安全模型叫 DAC, 全称是 Discretionary Access Control ，翻译为自主访问控制。
2. 后来又增加设计了一个新的安全模型叫 MAC, 全称是 Mandatory Access Control, 翻译为强制访问控制。

注意, MAC 和 DAC 不是互斥的， DAC 是最基本的安全模型，也是通常我们最常用到的访问控制机制是 Linux 必须具有的功能， 而 MAC 是构建在 DAC 之上的加强安全机制，属于可选模块。访问前， Linux 系统通常都是先做 DAC 检查， 如果没有通过则操作直接失败 ; 如果通过 DAC 检查并且系统支持 MAC 模块，再做 MAC 权限检查。

为区分两者，我们将支持 MAC 的 Linux 系统称作 SELinux, 表示它是针对 Linux 的安全加强系统。

DAC 安全模型
************************************

DAC 的核心内容是：在 Linux 中，进程理论上所拥有的权限与执行它的用户的权限相同。其中涉及的一切内容，都是围绕这个核心进行的。


用户和组 ID 信息控制
====================================

用户、组、口令信息

通过 /etc/passwd 和 /etc/group 保存用户和组信息，通过 /etc/shadow 保存密码口令及其变动信息， 每行一条记录。

用户和组分别用 UID 和 GID 表示，一个用户可以同时属于多个组，默认每个用户必属于一个与之 UID 同值同名的 GID 。

对于 /etc/passwd , 每条记录字段分别为 用户名: 口令（在 /etc/shadow 加密保存）：UID:GID（默认 UID）: 描述注释: 主目录: 登录 shell(第一个运行的程序)

对于 /etc/group ， 每条记录字段分别为 组名：口令（一般不存在组口令）：GID：组成员用户列表（逗号分割的用户 UID 列表）

对于 /etc/shadow ，每条记录字段分别为： 登录名: 加密口令: 最后一次修改时间: 最小时间间隔: 最大时间间隔: 警告时间: 不活动时间:

举例


.. highlight:: none

::

    [Linux]$ ls -l
    total 8
    drwxrwxr-x 2 glenn glenn 4096 Aug 30 21:41 images
    - rw-rw-r-- 1 glenn glenn  141 Jul 10 09:53 index.html

文件权限控制信息

文件类型

Linux 中的文件有如下类型：

    普通文件， 又包括文本文件和二进制文件， 可用 touch 创建；
    套接字文件， 用于网络通讯，一般由应用程序在执行中间接创建；
    管道文件是有名管道，而非无名管道， 可用 mkfifo 创建；
    字符文件和块文件均为设备文件， 可用 mknod 创建；
    链接文件是软链接文件，而非硬链接文件, 可用 ln 创建。

访问权限控制组

分为三组进行控制：

    user 包含对文件属主设定的权限
    group 包含对文件属组设定的权限
    others 包含对其他者设定的权限

可设定的权限

下面给出常见（但非全部）的权限值， 包括：

    r 表示具有读权限。
    w 表示具有写权限。
    x 一般针对可执行文件 / 目录，表示具有执行 / 搜索权限。
    s 一般针对可执行文件 / 目录，表示具有赋予文件属主权限的权限，只有 user 和 group 组可以设置该权限。
    t 一般针对目录，设置粘滞位后，有权限的用户只能写、删除自己的文件, 否则可写、删除目录所有文件。旧系统还表示可执行文件运行后将 text 拷贝到交换区提升速度。

举例

通过 ls -l 可以查看到其文件类型及权限，通过 chmod 修改权限。

举例来说，

输出中， 第 1 个字符表示文件类型，其中，普通文件 (-)、目录文件 (d)、套接字文件 (s)，管道文件 (p)，字符文件 (c)，块文件 (b)，链接文件 (l)； 第 2 个字符开始的 -rwxr-xr-x 部分表示文件的权限位，共有 9 位。

对于文件 /usr/bin/qemu-i386 , 这个权限控制的含义是：

    第 2~4 位的 rwx 表示该文件可被它的 owner （属主）以 r 或 w 或 x 的权限访问。
    第 5~7 位的 r-x 表示该文件可被与该文件同一属组的用户以 r 或 x 的权限访问
    第 8~10 位的 r-x 表示该文件可被其它未知用户以 r 或 x 的权限访问。

对于 test/, test2/, test3/ 设定的权限：

    r,w,x 权限对每一权限控制组的权限用一位 8 进制来表示； 例如： 755 表示 rwxr-xr-x 。
    s,t 权限会替代 x 位置显示；设定 s,t 权限则需在对应的、用于控制 r,w,x 的 8 进制权限控制组前追加数字； s 权限用于属主属组控制， t 用于其它控制。
    设定属主 s 需追加 4, 设定属组 s 追加 2, 设定其它者 t 权限追加 1 ； 例如前面对 test/ 设定 t, 则用 1775, 表示 rwxrwxr-t 。

进程权限控制信息

进程权限

对于进程，有如下属性与文件访问权限相关：

    effective user id : 进程访问文件权限相关的 UID （简写为 euid ）。
    effective group id : 进程访问文件权限相关的 GID （简写为 egid ）。
    real user id : 创建该进程的用户登录系统时的 UID （简写为 ruid ）。
    real group id : 创建该进程的用户登录系统时的 GID （简写为 rgid ）。
    saved set user id : 拷贝自 euid 。
    saved set group id : 拷贝自 egid 。

举例

我们可以使用 ps 和 top 选择查看具有 euid 和 ruid 的进程。或者通过 top 来查看进程的 euid 和 ruid

通过 top 来查看的例子：

首先输入 top 得到类似如下

Linux权限控制的基本原理Linux权限控制的基本原理

这里通过 -d 选项延长 top 的刷新频率便于操作。此处可见，只有 USER 字段，表示相应进程的 effective user id.

打开 read user id 的显示选项：

a. 在 top 命令运行期间，输入 f, 可以看见类似如下行：

b. 输入 c 即可打开 Real user name 的显示开关。

c. 最后 Return 回车回到 top 中，即可看到 real user id 的选项。此时输入`o`，可调整列次序。最终我们可看到包含`effective user id`和`real user id`的输出如下：
进程访问文件的权限控制策略

规则

进程访问文件大致权限控制策略

对于进程访问文件而言，最重要的是 euid, 所以其权限属性均以 euid 为 “中心”。

    进程的 euid 一般默认即为 其 ruid 值
    若可执行文件的可执行权限位为 s ，进程对其调用 exec 后，其 euid 被设置为该可执行文件的 user id
    进程的 saved set user id 拷贝自 euid.
    当进程的 euid 与文件的 user id 匹配时，进程才具有文件 user 权限位所设定的权限
    组权限 egid 的控制规则类似。

通过 exec 执行文件修改权限属性

通过 exec 调用可执行文件之时：

    进程 ruid 值始终不变；
    saved set-user ID 始终来自 euid ；
    euid 值取决于文件的 set-user-ID 位是否被设置。

如下：

通过 setuid(uid) 系统调用修改权限属性

通过 setuid(uid) 修改权限属性之时：

    superuser 可顺利修改 ruid, euid, saved set-user ID ；
    unprivileged user 只能在 uid 与 ruid 相等时修改 euid, 其它无法修改。

举例

再举几个比较特别的例子：

设置了 set-user-id

Linux权限控制的基本原理Linux权限控制的基本原理

如前所述，这个输出的含义是，对于 /usr/bin/sudo 文件，

    第 1~3 位的 rws 表示该文件可被它的 owner（属主）以 r 或 w 或 s 的权限访问
    第 4~6 位的 r-x 表示该文件可被与该文件同一属组的用户以 r 或 x 的权限访问。
    第 7~9 位的 r-x 表示该文件可被其它未知用户以 r 或 x 的权限访问。

这样设置之后，对于 owner，具有读、写、执行权限，这一点没有什么不同。但是对于不属于 root 组的普通用户进程来说，却大不相同。

普通用户进程执行 sudo 命令时通过其 others 中的 x 获得执行权限，再通过 user 中的 s 使得普通用户进程临时具有了 sudo 可执行文件属主 ( root ) 的权限，即超级权限。

这也是为什么通过 sudo 命令就可以让普通用户执行许多管理员权限的命令的原因。

设置了 stick-bit

Linux权限控制的基本原理Linux权限控制的基本原理

这样设置之后，对于 /tmp 目录，任何人都具有读、写、执行权限，这一点没有什么不同。但是对于 others 部分设置了粘滞位 t, 其功能却大不相同。

若目录没设置粘滞位，任何对目录有写权限者都则可删除其中任何文件和子目录，即使他不是相应文件的所有者，也没有读或写许可 ; 设置粘滞位后，用户就只能写或删除属于他的文件和子目录。

这也是为什么任何人都能向 /tmp 目录写文件、目录，却只能写和删除自己拥有的文件或目录的原因。

举一个 man 程序的应用片断，描述 set-user-id 和 saved set-user-id 的使用

man 程序可以用来显示在线帮助手册， man 程序可以被安装指定 set-user-ID 或者 set-group-ID 为一个指定的用户或者组。

man 程序可以读取或者覆盖某些位置的文件，这一般由一个配置文件 (通常是 /etc/man.config 或者 /etc/manpath.config ) 或者命令行选项来进行配置。

man 程序可能会执行一些其它的命令来处理包含显示的 man 手册页的文件。

为防止处理出错， man 会从两个特权之间进行切换：运行 man 命令的用户特权，以及 man 程序的拥有者的特权。

需要抓住的主线：当只执行 man 之时，进程特权就是 man 用户的特权， 当通过 man 执行子进程（如通过 !bash 引出 shell 命令）时，用户切换为当前用户，执行完又切换回去。

过程如下：

    假设 man 程序文件被用户 man 所拥有，并且已经被设置了它的 set-user-ID 位，当我们 exec 它的时候，我们有如下情况：
    – real user ID = 我们的用户 UID
    – effective user ID = man 用户 UID
    – saved set-user-ID = man 用户 UID
    man 程序会访问需要的配置文件和 man 手册页。这些文件由 man 用户所拥有，但是由于 effective user ID 是 man, 文件的访问就被允许了。
    在 man 为我们运行任何命令的时候，它会调用 setuid(getuid())) (getuid() 返回的是 real user id).
    因为我们不是 superuser 进程，这个变化只能改变 effective user ID. 我们会有如下情况：
    现在 man 进程运行的时候把我们得 UID 作为它的 effective user ID. 这也就是说，我们只能访问我们拥有自己权限的文件。也就是说，它能够代表我们安全地执行任何 filter.
    – real user ID = 我们的用户 UID(不会被改变)
    – effective user ID = 我们的用户 UID
    – saved set-user-ID = man 的用户 UID(不会被改变)
    当 filter 做完了的时候， man 会调用 setuid(euid).
    这里， euid 是 man 用户的 UID.(这个 ID 是通过 man 调用 geteuid 来保存的) 这个调用是可以的，因为 setuid 的参数和 saved set-user-ID 是相等的。(这也就是为什么我们需要 saved set-user-ID). 这时候我们会有如下情况：
    – real user ID = 我们的用户 UID(不会被改变)
    – effective user ID = man 的 UID
    – saved set-user-ID = man 的用户 UID(不会被改变)
    由于 effective user ID 是 man, 现在 man 程序可以操作它自己的文件了。
    通过这样使用 saved set-user-ID, 我们可以在进程开始和结束的时候通过程序文件的 set-user-ID 来使用额外的权限。然而，期间我们却是以我们自己的权限运行的。如果我们无法在最后切换回 saved set-user-ID, 我们就可能会在我们运行的时候保留额外的权限。

下面我们来看看如果 man 启动一个 shell 的时候会发生什么：

    这里的 shell 是 man 使用 fork 和 exec 来启动的。
    因为这时 real user ID 和 effective user ID 都是我们的普通用户 UID(参见 step3)， 所以 shell 没有其它额外的权限.
    启动的 shell 无法访问 man 的 saved set-user-ID(man) , 因为 shell 的 saved set-user-ID 是由 exec 从 effective user ID 拷贝过来的。
    在执行 exec 的子进程 ( shell ) 中，所有的 user ID 都是我们的普通用户 ID.

实际上，我们描述 man 使用 setuid 函数的方法不是特别正确，因为程序可能会 set-user-ID 为 root . 这时候， setuid 会把所有三种 uid 都变成你设置的 id，但是我们只需要设置 effective user ID。


