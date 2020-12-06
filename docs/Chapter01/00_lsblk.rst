.. _cmd_lsblk:

lsblk 列出块设备
####################################

lsblk（list block）列出所有可用块设备的信息，而且还能显示设备之间的依赖关系，但是不会列出 RAM 的信息。

.. hint::

    lsblk 命令包含在 util-linux包中，这个包带了几个其它工具，如 dmesg。如果系统中没有 lsblk 命令，请安装 util-linux 包。


命令格式：
************************************

.. highlight:: none

::

    lsblk [options] [device...]

    
常用选项：
************************************

::

    -a, --all
        显示所有设备,包括空设备

    -d, --nodeps
        不显示分区的详细信息

    -l, --list
        使用列表格式显示

    -m, --perms	
        显示设备权限信息

    -o, --output list
        指定输出信息，支持的选项
        NAME,SIZE,OWNER,GROUP,MODE,FSTYPE,LABEL,UUID,MOUNTPOINT...

    -S, --scsi
        只显示 SCSI 设备


使用实例：
************************************

::

    # 默认输出
    [Linux]$ lsblk 
    NAME        MAJ:MIN RM   SIZE RO TYPE MOUNTPOINT
    loop0         7:0    0  55.3M  1 loop /snap/core18/1885
    loop1         7:1    0  55.4M  1 loop /snap/core18/1932
    loop2         7:2    0 162.9M  1 loop /snap/gnome-3-28-1804/145
    loop3         7:3    0 255.6M  1 loop /snap/gnome-3-34-1804/36
    loop4         7:4    0  62.1M  1 loop /snap/gtk-common-themes/1506
    loop6         7:6    0    51M  1 loop /snap/snap-store/498
    loop7         7:7    0  31.1M  1 loop /snap/snapd/10238
    loop8         7:8    0 290.4M  1 loop /snap/vlc/1700
    loop9         7:9    0  64.8M  1 loop /snap/gtk-common-themes/1514
    loop10        7:10   0  49.8M  1 loop /snap/snap-store/467
    loop11        7:11   0 217.9M  1 loop /snap/gnome-3-34-1804/60
    loop12        7:12   0  31.1M  1 loop /snap/snapd/10492
    nvme0n1     259:0    0 238.5G  0 disk 
    ├─nvme0n1p1 259:1    0   487M  0 part /boot/efi
    ├─nvme0n1p2 259:2    0   1.9G  0 part [SWAP]
    └─nvme0n1p3 259:3    0 236.1G  0 part /

- NAME：设备名
- MAJ:MIN：主要/次要设备号
- RM：是否可移动设备（0 否，1 是）
- SIZE：设备的容量大小
- RO：是否为只读设备（0 否，1 是）
- TYPE：设备类型（disk 磁盘，part 分区，loop 块设备）
- MOUNTPOINT：设备挂载点

::

    # 查看指定设备
    [Linux]$ lsblk /dev/nvme0n1
    NAME        MAJ:MIN RM   SIZE RO TYPE MOUNTPOINT
    nvme0n1     259:0    0 238.5G  0 disk 
    ├─nvme0n1p1 259:1    0   487M  0 part /boot/efi
    ├─nvme0n1p2 259:2    0   1.9G  0 part [SWAP]
    └─nvme0n1p3 259:3    0 236.1G  0 part /

    # 自定义输出项
    [Linux]$ lsblk -o name,size,uuid /dev/nvme0n1
    NAME          SIZE UUID
    nvme0n1     238.5G 
    ├─nvme0n1p1   487M D379-5667
    ├─nvme0n1p2   1.9G 352ca1aa-5508-4303-9646-cac0e1d791ca
    └─nvme0n1p3 236.1G 22aba028-77ae-43a3-a5fb-5d9d05c99ad5

    [Linux]$ lsblk -o +uuid /dev/nvme0n1
    NAME        MAJ:MIN RM   SIZE RO TYPE MOUNTPOINT UUID
    nvme0n1     259:0    0 238.5G  0 disk            
    ├─nvme0n1p1 259:1    0   487M  0 part /boot/efi  D379-5667
    ├─nvme0n1p2 259:2    0   1.9G  0 part [SWAP]     352ca1aa-5508-4303-9646-cac0e1d791ca
    └─nvme0n1p3 259:3    0 236.1G  0 part /          22aba028-77ae-43a3-a5fb-5d9d05c99ad5
