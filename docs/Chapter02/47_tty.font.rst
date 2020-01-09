tty 终端字体
####################################

默认情况下，终端使用内核的内置字体，一般为点阵字体 [1]_ 其包含 CP437 字体集。

Linux 终端默认使用 UTF-8 编码，由于使用的是标准的兼容 VGA 的帧缓存，终端字体限定为 256 或 512 个字形。如果字体超出了256个字形，那么颜色的数量就会从 16 减少到 8。为了正确的显示符号，需要一个特殊的转换图（unimap）将符号和 Unicode 的值对应。现在大多数终端字体都具有内置的 unimap。

.. note::

    kbd 包提供了改变虚拟终端字体的工具。可以使用的字体存储在 ``/usr/share/kbd/consolefonts/`` 目录下（各发行版的存储路径略有不同，CentOS 下路径为 ``/lib/kbd/consolefonts`` ），那些以 ``.psfu`` 或者 ``.psfu.gz`` 结尾的字体具有内置的 Unicode 转换映射，可用于终端字体。

    psfu，psfu.gz – 点阵字体。 ``s`` 代表屏幕， ``f`` 代表字体， ``u`` 代表 Unicode， ``gz`` 用 gzip 压缩（不适用于X.Org）。


预览和测试
************************************

``showconsolefont`` 命令会以表格形式显示可用字与字符：

.. highlight:: none

::

    $ showconsolefont

``setfont`` 可以暂时改变字体（预览字体效果），只要指定字体名称即可（字体位于 /usr/share/kbd/consolefonts/），比如：

::

    $ setfont lat2-16

如果对新换的字体不满意，可以用 ``setfont`` 命令恢复至默认字体（就算终端显示乱码，将命令「盲打」执行依然有效）：

::

    $ setfont

.. note::

    setfont 只作用于当前正在使用的终端。其它终端无论活跃与否都不受影响。


配置文件
************************************

systemd-vconsole-setup 是一个用于配置虚拟控制台的辅助程序， 它既可以一次性配置所有虚拟控制台，也可以通过指定 TTY 参数单独配置某个特定的虚拟控制台。在系统启动过程中，初始化虚拟控制台时，它将被 systemd-udevd(8) 调用。 systemd-vconsole-setup.service 服务也会在内部调用该程序。 而该程序也会通过调用 loadkeys(1) 与 setfont(8) 来配置虚拟控制台的键盘映射与字体。

在修改 vconsole.conf 之后，既可以通过直接调用此程序来刷新控制台的设置， 也可以通过 systemctl restart 或 systemctl start 命令来刷新控制台的设置。

``/etc/vconsole.conf`` 的 ``FONT`` 变量可以用来在启动时设置字体，对于所有终端都有效。如果没用 ``vconsole.conf`` 配置文件，手动创建即可。

注意：配置文件的位置各发行版的位置不尽相同，在 Debian 中的配置文件为 ``/etc/default/console-setup``

若要显示 ``Č, ž, đ, š, Ł, ę, ą, ś`` 之类的字符，请使用 lat2-16.psfu.gz 这个字体：

::

    /etc/vconsole.conf

    ...
    FONT=lat2-16

这代表使用 ISO/IEC 8859 字符的第二部分，尺寸设置为 16。你可以使用其它值更改字体尺寸（如 lat2-08）。

如果开机时字体没有任何变化，或只变化一下就恢复原样，有可能是因为显卡驱动引导时字体被复位，然后终端被切至帧缓冲 (framebuffer)。提前加载图形驱动可以避免这个问题。


调整控制台字体太小的方法
************************************

Linux 系统默认的终端字体大小为 16px，在高分辨率屏幕下的显示效果惨不忍睹。
上边已经提到了，由于点阵字体只能清晰地显示在相应的字号下，所以要改变字体的大小，最好是调整字体。

1. 下载 terminus 字体，官网链接： http://terminus-font.sourceforge.net/。

2. 解压文件：

::

    cd ~/Download
    tar -zxvf terminus-font-4.46.tar.gz
    cd terminus-font-4.46

3. 安装字体：

::

    sudo su  # 提升权限
    ./configure --psfdir=/usr/share/kbd/consolefonts/
    make -j4 psf  # 编译字体
    make install-psf  # 安装字体

    # 以下可选
    make -j4 psf-vgaw
    make install-psf-vgaw

4. 预览字体效果：

::

    cd /usr/share/kbd/consolefonts/
    ls
    # 会多出好多 ter-XXXX.psf.gz 的字体文件
    # 预览字体效果
    setfont ter-932n.psf.gz

ter-932n.psf.gz 其中 9 是代号，32 代表高度，n 表示 normal，还有 b 表示 bold 等。


.. note::

    点阵字体在处理 ``0 o O 1 l i`` 等字符时十分容易混淆，这里推荐另一款字体：http://www.fial.com/~scott/tamsyn-font/ 或 http://pelulamu.net/unscii/

    将 *.psf.gz 文件放在字体目录下，在配置时请使用绝对路径指定字体。


.. [1] 点阵字体：每种字形的每种形式和每种尺寸的图像都由点或者像素组成的矩阵构成。由于位图的原故，点阵字体只能清晰地显示在相应的字号下，很难进行缩小和放大。对于太小的字，点阵字体常常比其它类型的字体有更好的显示效果。常见的点阵字体格式有 bdf，pcf，fnt，hbf 等。
