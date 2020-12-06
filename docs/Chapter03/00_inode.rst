inode 索引节点
####################################

inode 是理解 Unix/Linux 文件系统和硬盘储存的基础。

理解 inode，不仅有助于提高系统操作水平，还有助于体会 Unix 设计哲学，即如何把底层的复杂性抽象成一个简单概念，从而大大简化用户接口。


inode 是什么？
************************************

理解 inode，要从文件储存说起。文件储存在硬盘上，硬盘的最小存储单位叫做"扇区"（Sector），每个扇区储存 512 字节（相当于 0.5KB）。

操作系统读取硬盘的时候，不会一个扇区一个扇区地读取，这样效率太低。而是一次性连续读取多个扇区，即一次性读取一个"块"（block），这种由多个扇区组成的"块"，是文件存取的最小单位。"块"的大小通常是 4KB，即连续八个 sector 组成一个 block。

文件数据都储存在"块"中，那么还需要一个地方储存文件的元信息，比如文件的创建者、文件的创建日期、文件的大小等。这种储存文件元信息的区域就叫做 inode，中文译名为"索引节点"。

每一个文件都有对应的 inode，里面包含了与该文件有关的信息。


inode 的内容
************************************

inode 包含文件的元信息，具体来说有以下内容：

* 文件的字节数
* 文件拥有者的 User ID
* 文件用户组的 Group ID
* 文件的读、写、执行权限
* 文件的时间戳，共有三个：
    - ctime 指 inode 上一次变动的时间
    - mtime 指文件内容上一次变动的时间
    - atime 指文件上一次打开的时间
* 链接数，即有多少文件名指向这个 inode
* 文件数据 block 的位置

可以用  :ref:`stat <cmd_stat>` 命令，查看某个文件的 inode 信息：

.. highlight:: none

::

    [Linux]$ stat abc.txt
      File: 'abc.txt'
      Size: 7805      Blocks: 16         IO Block: 4096   regular file
    Device: fc09h/64521dInode: 152067      Links: 1
    Access: (0664/-rw-rw-r--)  Uid: ( 5000/shiyanlou)   Gid: ( 5000/shiyanlou)
    Access: 2018-01-16 15:14:20.419663570 +0800
    Modify: 2018-01-16 15:14:28.331874373 +0800
    Change: 2018-01-16 15:14:28.331874373 +0800
     Birth: -


总之，除了文件名以外的所有文件信息，都存在 inode 之中。至于为什么没有文件名，下文会有详细解释。


inode 的大小
************************************

inode 也会消耗硬盘空间，所以在格式化硬盘的时候，操作系统自动将硬盘分成两个区域。一个是数据区，存放文件数据；另一个是 inode 区（inode table），存放 inode 所包含的信息。

inode 节点一般占用 128 字节或 256 字节。inode 节点的总数，在格式化时会自动设定。一般是每 1KB 或每 2KB 就设置一个 inode。假定在一块 1GB 的硬盘中，每个 inode 节点的大小为 128 字节，每 1KB 就设置一个 inode，那么 inode table 的大小就会达到 128MB，占整块硬盘的 12.8%。

查看每个硬盘分区的 inode 总数和已经使用的数量，可以使用 :ref:`df <cmd_df>` 命令。

::

    [Linux]$ df -i
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


查看每个 inode 节点的大小，可以用如下命令（需要 root 权限）：

::

    [Linux]# dumpe2fs -h /dev/hda | grep "Inode size"
    dumpe2fs 1.41.14 (22-Dec-2010)
    Inode size:                256


由于每个文件都必须有一个 inode，因此有可能发生 inode 已经用光，但是硬盘还未存满的情况。这时，就无法在硬盘上创建新文件。


inode 号码
************************************

每个 inode 都有一个号码，操作系统用 inode 号码来识别不同的文件。

这里值得重复一遍，Unix/Linux 系统内部不使用文件名，而使用 inode 号码来识别文件。对于系统来说，文件名只是 inode 号码便于识别的别称或者绰号。

表面上，用户通过文件名打开文件。实际上，系统内部将这个过程分成三步：

1. 系统找到文件名对应的 inode 号码
2. 通过 inode 号码，获取 inode 信息
3. 根据 inode 信息，找到文件数据所在的 block，读出数据。

使用 :ref:`ls -l <cmd_ls>` 命令查看文件名对应的 inode 号码：

::

    [Linux]$ ls -i abc.txt
    152067 abc.txt


目录文件
************************************

Unix/Linux 系统中，目录（directory）也是一种文件。打开目录，实际上就是打开目录文件。

目录文件的结构非常简单，就是一系列目录项（dirent）的列表。每个目录项，由两部分组成：所包含文件的文件名，以及该文件名对应的 inode 号码。所以在使用 :ref:`ls -lh <cmd_ls>` 命令查看文件夹大小时，所有的文件夹只有 4KB 大小。

ls 命令只列出目录文件中的所有文件名：

::

    [Linux]$ ls -lh
    total 48K
    drwxr-xr-x  2 glenn glenn 4.0K Jul  9 14:59 Desktop
    drwxr-xr-x 10 glenn glenn 4.0K Dec  3 17:39 Documents
    drwxr-xr-x  2 glenn glenn  12K Nov 30 13:46 Downloads
    drwxr-xr-x  3 glenn glenn 4.0K Nov 30 14:53 Music
    drwxr-xr-x  2 glenn glenn 4.0K Nov 30 14:54 Pictures
    drwxr-xr-x  2 glenn glenn 4.0K Nov 22 20:33 Public
    drwxr-xr-x  2 glenn glenn 4.0K Jul  9 14:59 Templates
    drwxr-xr-x  8 glenn glenn 4.0K Oct 19 22:52 Videos


如果要查看文件的详细信息，就必须根据 inode 号码，访问 inode 节点，读取信息。

.. note:: 目录的执行权限

    目录文件的读权限（r）和写权限（w）只是针对目录文件本身。由于目录文件内只有文件名和 inode 号码，所以如果只有读权限，只能获取文件名，无法获取其他信息。
    
    ::
    
        [Linux]$ ls -l 
        drw-rw-rw-  2 glenn glenn  4096 Dec  5 18:11 test/
        [Linux]$ls -l test/
        total 0
        d????????? ? ? ? ?            ? ./
        d????????? ? ? ? ?            ? ../
        -????????? ? ? ? ?            ? b.txt

    要读取 inode 节点内的信息具有文件夹的执行权限（x）。


inode 的特殊作用
************************************

由于 inode 号码与文件名分离，这种机制导致了一些 Unix/Linux 系统特有的现象。

1. 当文件名包含特殊字符，无法正常删除时。可以删除 inode 节点，就能直接删除文件。
2. 移动文件或重命名文件，只是改变文件名，不影响 inode 号码。所以在 Linux 中移动文件不论大小基本秒成。
3. 打开一个文件后，系统就以 inode 号码来识别文件，不再考虑文件名。因此，系统无法从 inode 号码得知文件名。

第 3 点使得在更新软件时可以不关闭、不重启软件。因为系统通过 inode 号码，识别运行中的文件，软件更新的时候，新版文件以同样的文件名，生成一个新的 inode，不会影响到运行中的文件。等到下一次运行这个软件的时候，文件名就自动指向新版文件，旧版文件的 inode 则被回收。
