aptitude 安装软件
##########################

aptitude 与 apt-get 一样，是 Debian 及其衍生系统中功能极其强大的包管理工具。与 apt-get 不同的是，aptitude 在处理依赖问题上更佳一些。举例来说，aptitude 在删除一个包时，会同时删除本身所依赖的包。这样系统中不会残留无用的包，整个系统更为干净。

命令格式：
***********************

.. highlight:: none

::

    *****

常用选项：
***********************

============================     ==============
命令                                说明
============================     ==============
aptitude update                     更新可用的包列表
aptitude upgrade                    升级可用的包
aptitude install pkgname            安装包
aptitude remove pkgname             删除包
aptitude purge pkgname              删除包及其配置文件
aptitude search string              搜索包
aptitude show pkgname               显示包的详细信息
aptitude clean                      删除下载的包文件
aptitude autoclean                  仅删除过期的包文件
============================     ==============

在终端中直接输入 aptitude 命令，会启动一个图形化的安装工具。aptitude 的主视图组织的非常有条理，而且界面布局清爽。上部窗口列出的是软件包根据类别和它们在本地系统的状态排序。

在 aptitude 软件包列表中的软件包状态：

==========   ==========
状态            说明
==========   ==========
v              虚拟
B              损坏
u              解包
C              预配置
H              预安装
c              卸载未清除
p              清除软件包
i              已经安装
E              内部错误
==========   ==========

在 aptitude 软件包列表中的请求操作：

==========   ==========
状态            说明
==========   ==========
h              保持
p              清除
d              删除（卸载）
B              损坏
i              安装
r              重装
u              升级
==========   ==========

使用实例：
***********************

::

    # 安装 samba 服务
    [root@localhost ~]# aptitude install samba
