树莓派 ZERO W 配置
####################################

树莓派 Zero W 是树莓派基金会 17 年 2 月底为庆祝其第五个生日发布的特别版，本质上是树莓派 Zero 加无线和蓝牙。完整配置如下：1GHz 单核 CPU，512MB 内存，mini-HDMI 接口，Micro-USB OTG 端口，Micro-USB 电源接口，HAT 兼容 40 针 GPIO 扩展口，复合视频和重置接头，CSI 摄像机连接器，802.11n WiFi 和蓝牙 4.1（BLE）。


安装系统镜像
************************************

软件准备：Raspbrain 系统镜像文件，SDFormatter，Win32diskim，PuTTY。

1. 下载 `Raspbrain 系统 <https://www.raspberrypi.org/downloads/raspbian/>`_ ，建议下载 ``Raspberry Pi OS Lite`` 不带图形界面的版本。

2. 下载 `Pi Imager <https://www.raspberrypi.org/software/>`_  SD 卡烧录工具，并安装到电脑。

3. 将 SD 卡插入读卡器然后接入电脑，用 Pi Imager 烧录系统：

   - 点击 ``CHOOSE OS`` 选择 ``Use custom`` 然后选择下载到本地的系统
   - 点击 ``CHOOSE SD CARD`` 选择 SD 卡的路径
   - 点击 ``WRITE`` 写入系统。
   - 最后点击 ``CONTINUE`` 完成系统写入。

.. hint::

    系统写入会提示格式化 SD 卡的选项，确认就可以。

    系统写入完成之后，SD 卡会被分成两个分区，一个是 ``/boot`` 分区，一个是 ``/`` 分区。在 Windows 系统中是不会识别根分区的，会提示需要格式化分区，千万不要点击格式化，忽略即可。


无线连接
====================================

在 SD 卡的 ``/boot`` 分区中新建 ``wpa_supplicant.conf`` 文件，输入如下内容并保存。

.. highlight:: none

::

    # This is /boot/wpa_supplicant.conf
    ctrl_interface=DIR=/var/run/wpa_supplicant GROUP=netdev
    update_config=1
    country=CN
    network={
        ssid="WiFi名称1"
        psk="WiFi密码1"
        priority=5
    }


当系统开机后， ``/boot`` 分区中的配置文件会消失，再次配置请参阅 :doc:`wpa_supplicant 连接 wifi <../Chapter02/32_wpa.supplicant>` 


开启 SSH 远程连接
====================================

在 SD 卡的 ``/boot`` 分区中新建命名为 ``ssh`` 的文件，内容为空。


上电开机
====================================

将 SD 卡插入树莓派的 SD 卡槽，连接电源线自动开机。

使用 SSH 登陆，默认用户名 ``pi`` ，默认密码 ``raspberry`` 。


配置系统
************************************

修改默认密码
====================================

使用 ``passwd`` 命令修改密码。

::

    # 修改 pi 密码
    [Linux]$ sudo passwd pi
    重复输入两次密码

    # 修改 root 密码，开启 root 账号
    [Linux]$ sudo passwd root


修改清华大学软件源
====================================

在配置文件 ``/etc/apt/sources.list`` 中注释其他行，加入以下软件源：

::

    deb http://mirrors.tuna.tsinghua.edu.cn/raspbian/raspbian/ buster main non-free contrib rpi
    deb-src http://mirrors.tuna.tsinghua.edu.cn/raspbian/raspbian/ buster main non-free contrib rpi


在配置文件 ``/etc/apt/sources.list.d/raspi.list`` 中注释其他行，加入以下软件源：

::

    deb http://mirrors.tuna.tsinghua.edu.cn/raspberrypi/ buster main ui


网址末尾的 raspbian 重复两次是必须的。因为 Raspbian 的仓库中除了 APT 软件源还包含其他代码。APT 软件源不在仓库的根目录，而在 ``raspbian/`` 子目录下。

编辑镜像站后，请使用 ``sudo apt-get update`` 命令，更新软件源列表，同时检查您的编辑是否正确。


禁用自动休眠
====================================

树莓派默认开启了自动休眠，可以使用 systemctl 命令来禁用自动休眠。

::

    [Linux]$ sudo systemctl mask sleep.target suspend.target hibernate.target hybrid-sleep.target
    Created symlink /etc/systemd/system/sleep.target → /dev/null.
    Created symlink /etc/systemd/system/suspend.target → /dev/null.
    Created symlink /etc/systemd/system/hibernate.target → /dev/null.
    Created symlink /etc/systemd/system/hybrid-sleep.target → /dev/null.


修改时区
====================================

通过修改时区的链接文件来修改时区，修改为上海时间。

::

    [Linux]$ sudo ln -sf /usr/share/zoneinfo/Asia/Shanghai  /etc/localtime


修改系统语言
====================================

以 root 身份运行：

::

    [Linux]# dpkg-reconfigure locales
