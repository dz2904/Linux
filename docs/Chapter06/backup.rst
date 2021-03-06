系统备份与还原
############################

在一个折腾无极限的年纪，手残、尝鲜、测试...谁都不能保证你的 Linux 每次都能顺利启动，提前做好系统备份会是一个明智的选择。

网上有好多的 ``tar`` 命令备份方法，但是按方法测试后一次都没有成功过。
转而求其次，使用 ``dd`` 命令备份系统，简单直接。缺点是装系统时需要提前设计分区，系统备份需要占用单独的分区。

操作步骤
****************************

1. 使用 ``df -h`` 命令查看系统分区，确定根目录的磁盘号
2. 使用 ``dd if=/dev/sda of=/dev/sdb`` 命令备份磁盘分区，sda 为根分区，sdb 为备份分区

两步系统就备份完成，如果需要还原系统，只需要颠倒分区位置 ``dd if=/dev/sdb of=/dev/sda`` 。


推荐阅读： :doc:`../Chapter01/00_dd`
