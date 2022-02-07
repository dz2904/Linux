xfce4+i3 环境配置
####################################

i3wm 是一个极简主义的平铺管理器，它能以无缝、不重叠的方式智能地排列屏幕上的窗口。其他平铺管理器包括 xmonad 和 wmii 等。

i3wm 窗口管理器底层使用 Xorg server，xfce4.16 也使用 Xorg server 底层操作，所以两者可以无缝的衔接。

i3wm 状态栏配置繁琐，直接使用 xfce4 的状态栏配置，简洁高效。

操作环境要求：

- Debian11 操作系统
- xfce4.16 桌面环境


状态栏配置
************************************

启动 i3 窗口管理器后，默认的配置文件目录是 ``~/.config/i3/config`` 。

.. highlight:: none

::

    1. 注释掉 i3 默认的状态栏配置

    # bar {
    #         status_command i3status
    # }


    2. 添加 xfce4 状态栏

    # 添加 xfce4 状态栏
    exec --no-startup-id xfce4-panel --disable-wm-check
    # 启动 xfce4 电源管理模块
    exec --no-startup-id xfce4-power-manager

一些细节调整：

1. 在状态栏中加入多桌面，并设置成按钮，关闭桌面名显示。
2. 输入法图标强制大小显示。
3. 时间设置成 24 小时带秒显示。


桌面背景
************************************

xfce4 默认使用 LightDM 登陆管理器。修改登陆管理器背景后会同步到桌面中。

::

    /etc/lightdm/lightdm-gtk-greeter.conf
    -----------------------------------------------------------

    [greeter]
    background=/usr/share/pixmaps/bg.jpg


触控板点击和自然滚动
************************************

::

    /usr/share/X11/xorg.conf.d/40-libinput.conf
    -----------------------------------------------------------

    Section "InputClass"
            Identifier "libinput touchpad catchall"
            MatchIsTouchpad "on"
            MatchDevicePath "/dev/input/event*"
            Driver "libinput"
            # 添加以下两行，第一行开启点击，第二行双指自然滚动
            Option "Tapping" "on"
            Option "NaturalScrolling" "true"
    EndSection


已知问题
************************************

- xfce4 锁屏管理器有 bug，锁屏后不能登陆。在设置禁用锁屏功能
- Debian11 暂时不支持 i3-gaps，设置窗口间隙。
