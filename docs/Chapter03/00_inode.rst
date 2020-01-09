inode 索引节点
####################################

inode_ 是理解 Unix/Linux 文件系统和硬盘储存的基础。

.. _inode: https://en.wikipedia.org/wiki/Inode

我觉得，理解 inode，不仅有助于提高系统操作水平，还有助于体会 Unix 设计哲学，即如何把底层的复杂性抽象成一个简单概念，从而大大简化用户接口。


inode是什么？
************************************

理解 inode，要从文件储存说起。文件储存在硬盘上，硬盘的最小存储单位叫做"扇区"（Sector），每个扇区储存 512 字节（相当于 0.5KB）。

操作系统读取硬盘的时候，不会一个扇区一个扇区地读取，这样效率太低，而是一次性连续读取多个扇区，即一次性读取一个"块"（block）。这种由多个扇区组成的"块"，是文件存取的最小单位。"块"的大小，最常见的是 4KB，即连续八个 sector 组成一个 block。

文件数据都储存在"块"中，那么很显然，我们还必须找到一个地方储存文件的元信息，比如文件的创建者、文件的创建日期、文件的大小等等。这种储存文件元信息的区域就叫做 inode，中文译名为"索引节点"。

每一个文件都有对应的 inode，里面包含了与该文件有关的信息。


inode 的内容
************************************

inode 包含文件的元信息，具体来说有以下内容：

* 文件的字节数；

* 文件拥有者的 User ID；

* 文件用户组的 Group ID；

* 文件的读、写、执行权限；

* 文件的时间戳，共有三个：ctime 指 inode 上一次变动的时间，mtime 指文件内容上一次变动的时间，atime 指文件上一次打开的时间；

* 链接数，即有多少文件名指向这个 inode；

* 文件数据 block 的位置；

可以用 stat 命令，查看某个文件的 inode 信息：

.. highlight:: none

::

    [root@rudder ~]$ stat abc.txt
      File: 'abc.txt'
      Size: 7805      Blocks: 16         IO Block: 4096   regular file
    Device: fc09h/64521dInode: 152067      Links: 1
    Access: (0664/-rw-rw-r--)  Uid: ( 5000/shiyanlou)   Gid: ( 5000/shiyanlou)
    Access: 2018-01-16 15:14:20.419663570 +0800
    Modify: 2018-01-16 15:14:28.331874373 +0800
    Change: 2018-01-16 15:14:28.331874373 +0800
     Birth: -
    [root@rudder ~]$


总之，除了文件名以外的所有文件信息，都存在inode之中。至于为什么没有文件名，下文会有详细解释。


inode 的大小
************************************

inode 也会消耗硬盘空间，所以硬盘格式化的时候，操作系统自动将硬盘分成两个区域。一个是数据区，存放文件数据；另一个是 inode 区（inode table），存放 inode 所包含的信息。

每个 inode 节点的大小，一般是 128 字节或 256 字节。inode 节点的总数，在格式化时就给定，一般是每 1KB 或每 2KB 就设置一个 inode。假定在一块 1GB 的硬盘中，每个 inode 节点的大小为128字节，每 1KB 就设置一个 inode，那么 inode table 的大小就会达到 128MB，占整块硬盘的 12.8%。

查看每个硬盘分区的 inode 总数和已经使用的数量，可以使用 df 命令。

::

    [root@rudder ~]$ df -i
    Filesystem
       Inodes  IUsed    IFree IUse%  Mounted on
    /dev/mapper/docker
      786432  109887   676545   14% /
    tmpfs
      1021949     17  1021932    1% /dev
    tmpfs
      1021949     11  1021938    1% /sys/fs/cgroup
    /dev/xvdc1
     19660800  10120 19650680    1% /etc/hosts
    shm
      1021949      1  1021948    1% /dev/shm
    [root@rudder ~]$


查看每个inode节点的大小，可以用如下命令(需要 root 权限)：

::

    [root@rudder ~]# dumpe2fs -h /dev/hda | grep "Inode size"
    dumpe2fs 1.41.14 (22-Dec-2010)
    Inode size:                256
    [root@rudder ~]#


由于每个文件都必须有一个 inode，因此有可能发生 inode 已经用光，但是硬盘还未存满的情况。这时，就无法在硬盘上创建新文件。


inode 号码
************************************

每个 inode 都有一个号码，操作系统用 inode 号码来识别不同的文件。

这里值得重复一遍，Unix/Linux 系统内部不使用文件名，而使用 inode 号码来识别文件。对于系统来说，文件名只是 inode 号码便于识别的别称或者绰号。

表面上，用户通过文件名，打开文件。实际上，系统内部这个过程分成三步：首先，系统找到这个文件名对应的 inode 号码；其次，通过 inode 号码，获取 inode 信息；最后，根据 inode 信息，找到文件数据所在的 block，读出数据。

使用 ls -i 命令，可以看到文件名对应的 inode 号码：

::

    [root@rudder ~]$ ls -i abc.txt
    152067 abc.txt
    [root@rudder ~]$


目录文件
************************************

Unix/Linux 系统中，目录（directory）也是一种文件。打开目录，实际上就是打开目录文件。

目录文件的结构非常简单，就是一系列目录项（dirent）的列表。每个目录项，由两部分组成：所包含文件的文件名，以及该文件名对应的 inode 号码。

ls 命令只列出目录文件中的所有文件名：

::

    [root@rudder ~]$ ls
    Code  Desktop  abc.txt
    [root@rudder ~]$


ls -i命令列出整个目录文件，即文件名和 inode 号码：

::

    [root@rudder ~]$ ls -i
    152409 Code  152410 Desktop  152067 abc.txt
    [root@rudder ~]$


如果要查看文件的详细信息，就必须根据 inode 号码，访问 inode 节点，读取信息。ls -l命令列出文件的详细信息。

::

    [root@rudder ~]$ ls -l
    total 16
    drwxrwxr-x 2 shiyanlou shiyanlou 4096 Aug 17  2016 Code
    drwxrwxr-x 2 shiyanlou shiyanlou 4096 Aug 17  2016 Desktop
    -rw-rw-r-- 1 shiyanlou shiyanlou 7805 Jan 16 15:14 abc.txt
    [root@rudder ~]$


理解了上面这些知识，就能理解目录的权限。目录文件的读权限（r）和写权限（w），都是针对目录文件本身。由于目录文件内只有文件名和 inode 号码，所以如果只有读权限，只能获取文件名，无法获取其他信息，因为其他信息都储存在inode节点中，而读取inode节点内的信息需要目录文件的执行权限（x）。


硬链接
************************************

一般情况下，文件名和 inode 号码是"一一对应"的关系，每个 inode 号码对应一个文件名。但是，Unix/Linux 系统允许，多个文件名指向同一个 inode 号码。

这意味着，可以用不同的文件名访问同样的内容；对文件内容进行修改，会影响到所有文件名；但是，删除一个文件名，不影响另一个文件名的访问。这种情况就被称为"硬链接"（hard link）。

ln 命令可以创建硬链接：

::

    [root@rudder ~]$ ln abc.txt def.txt
    [root@rudder ~]$ ls -i
    152409 Code  152410 Desktop  152067 abc.txt  152067 def.txt
    [root@rudder ~]$


运行上面的命令后，多出来一个 def.txt 文件，而且两个文件的 inode 号相同。inode 信息中有一项叫做"链接数"，记录指向该 inode 的文件名总数，这时就会增加 1。

::

    [root@rudder ~]$ ls -li
    total 24
    152410 drwxrwxr-x 2 shiyanlou shiyanlou 4096 Aug 17  2016 Desktop
    152067 -rw-rw-r-- 2 shiyanlou shiyanlou 7805 Jan 16 15:14 abc.txt
    152067 -rw-rw-r-- 2 shiyanlou shiyanlou 7805 Jan 16 15:14 def.txt
    [root@rudder ~]$


反过来，删除一个文件名，就会使得 inode 节点中的"链接数"减 1。当这个值减到 0，表明没有文件名指向这个 inode，系统就会回收这个 inode 号码，以及其所对应 block 区域。

这里顺便说一下目录文件的"链接数"。创建目录时，默认会生成两个目录项："."和".."。前者的 inode 号码就是当前目录的 inode 号码，等同于当前目录的"硬链接"；后者的 inode 号码就是当前目录的父目录的 inode 号码，等同于父目录的"硬链接"。所以，任何一个目录的"硬链接"总数，总是等于2加上它的子目录总数（含隐藏目录）。


软链接
************************************

除了硬链接以外，还有一种特殊情况。

文件A和文件B的inode号码虽然不一样，但是文件A的内容是文件B的路径。读取文件A时，系统会自动将访问者导向文件B。因此，无论打开哪一个文件，最终读取的都是文件B。这时，文件A就称为文件B的"软链接"（soft link）或者"符号链接（symbolic link）。

这意味着，文件A依赖于文件B而存在，如果删除了文件B，打开文件A就会报错："No such file or directory"。这是软链接与硬链接最大的不同：文件A指向文件B的文件名，而不是文件B的inode号码，文件B的inode"链接数"不会因此发生变化。

ln -s 命令可以创建软链接：

::

    [root@rudder ~]$ ln -s abc.txt aaa
    [root@rudder ~]$ ls -l
    total 8
    lrwxrwxrwx 1 shiyanlou shiyanlou    7 Jan 16 17:49 aaa -> abc.txt
    -rw-rw-r-- 1 shiyanlou shiyanlou 7805 Jan 16 17:49 abc.txt
    [root@rudder ~]$


inode 的特殊作用
************************************

由于 inode 号码与文件名分离，这种机制导致了一些 Unix/Linux 系统特有的现象。

1. 有时，文件名包含特殊字符，无法正常删除。这时，直接删除 inode 节点，就能直接删除文件。

2. 移动文件或重命名文件，只是改变文件名，不影响 inode 号码。

3. 打开一个文件以后，系统就以 inode 号码来识别这个文件，不再考虑文件名。因此，系统无法从 inode 号码得知文件名。

第3点使得在更新软件时可以不关闭、不重启软件。因为系统通过 inode 号码，识别运行中的文件，软件更新的时候，新版文件以同样的文件名，生成一个新的 inode，不会影响到运行中的文件。等到下一次运行这个软件的时候，文件名就自动指向新版文件，旧版文件的 inode 则被回收。
