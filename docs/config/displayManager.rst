登陆管理器
##################################

登录管理器也叫显示管理器（Display Manager），它会提供给用户一个图形化的登录界面。当用户输入正确的用户名和密码时，登录管理器会开始一个会话（窗口管理器或者桌面环境）。 一个图形服务器只能由一个登录管理器来管理，但是系统中可以安装多个登录管理器。

显示管理器列表
***********************************

**控制台**

- CDM_ ：控制台显示管理器

- `Console TDM`_ ：扩展自xinit，由纯粹的Bash脚本编写的

- nodm_ ：支持自动登录的简单显示管理器。

**桌面环境**

- GDM_ ：GNOME 显示管理器。

- LightDM_ ：跨桌面的显示管理器，可以使用各种前端写的任何工具。

- LXDM_ ：LXDE 显示管理器 (独立于桌面环境) (lxdm)

- MDM：使用在Linux Mint中的显示管理器,GDM2的分支项目。

- SDDM_ ：基于QML的显示管理器，替代KDE4的KDM，推荐搭配Plamsa5或LXQt使用。

- XDM_ ：X 显示管理器支持XDMCP（适合服务器的宿主机）。

.. _CDM: https://github.com/ghost1227/cdm

.. _`Console TDM`: http://code.google.com/p/t-display-manager/

.. _nodm: http://enricozini.org/sw/nodm/

.. _GDM: http://projects.gnome.org/gdm/gdm

.. _LightDM: https://www.freedesktop.org/wiki/Software/LightDM/

.. _LXDM: https://en.wikipedia.org/wiki/LXDE

.. _SDDM: https://github.com/sddm/sddmsddm

.. _XDM: http://www.x.org/archive/X11R7.5/doc/man/man1/xdm.1.html


加载显示管理器
***********************************

以 SDDM 为例，配置开机启动：

.. highlight:: none

::

    # systemctl enable sddm.service


执行上述命令后，登录管理器应当能正常工作了。如果不是的话，可能是 default.target 没有指向 graphical.target。

启用 SDDM 后， ``/etc/systemd/system/`` 应该创建 display-manager.service 软链接，可以用 --force 覆盖已有链接。

::

    $ ls -l /etc/systemd/system/display-manager.service

    [...] /etc/systemd/system/display-manager.service -> /usr/lib/systemd/system/sddm.service


LightDM ( Light Display Manager )
***********************************

LightDM 是一个跨桌面显示管理器，其目的是成为 X org X 服务器的标准显示管理器。我们之所以编写一个新的显示管理器，是因为从 XDM 以来出现了很多新的显示管理器（通常基于 XDM 源代码），但这些项目之间的差别很小，主要是在GUI（比如说不同的开发工具包） 和性能上面的差别。我们想用一个通用的显示管理器来管理它们，并允许各自的差异。

**它的特点有:**

- 跨桌面 - 支持不同的桌面环境。

- 支持多种显示技术(X，Wayland, ...)。

- 轻量级 - 低内存使用，高性能。

- 支持定制会话。

- 支持远程登录(XDMCP，VNC，XDMCP，可插拔)。

**主要配置文件：**

::

    /usr/share/lightdm/lightdm.conf.d/*.conf
    /etc/lightdm/lightdm.conf.d/*.conf
    /etc/lightdm/lightdm.conf


启用自动登录
=======================================

编辑 LightDM 配置文件 ``/etc/lightdm/lightdm.conf`` ，取消 autologin-user 的注释，并添加要自动登陆的用户名:

::

    autologin-user=username


你必须是 autologin 组的成员才能使用自动登录:

::

    # groupadd autologin
    # gpasswd -a username autologin


命令行工具
=======================================

LightDM 提供一个命令行工具, dm-tool. 它可用来锁定当前 seat, 切换会话，等等。这对'极简'窗口管理器和测试非常有用。要列出可用命令，运行:

::

    $ dm-tool --help


更改背景图片/颜色
=======================================

如果您想使用一个纯色 (非图片) 的背景，只需将 background 变量设置为十六进制的颜色。

例如:

::

    background=#000000


如果需要在 greeter 上使用自定义图片，请修改 ``/etc/lightdm/lightdm.conf`` 中的 background 变量值。例如:

::

    background=/usr/share/pixmaps/black_and_white_photography-wallpaper-1920x1080.jpg


注意: 建议将 PNG 或 JPG 文件放到 ``/usr/share/pixmaps`` ，因为 LightDM 用户需要有背景文件的读取权限。


改变你的头像
=======================================

首先确保已安装 accountsservice 软件包，然后如下设置，把 username 替换为目标用户的登录名。文件名中不要加 ``.png`` 后缀:

::

    [User]
    Icon=/var/lib/AccountsService/icons/username

注意: 头像格式最好为PNG，确保文件的权限为 644 。
