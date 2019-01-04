安装 Debian 9 缺少无线网卡固件
#############################################

笔记本电脑安装 Debian 9，检测网卡时出现以下错误提示：

.. highlight:: none

::

    Some of your hardware needs non-free firmware files to operate. 
    The firmware can be loaded from removable media, such as a USB stick or floopy.

    The missing firmware files are: iwlwifi-5000-5.ucode iwlwifi-5000-4.ucode 
    iwlwifi-5000-3.ucode iwlwifi-5000-2.ucode iwlwifi-5000-1.ucode

    If you have such media available now, insert it, and continue.

    Load missing firmware from removable media?

    <Yes>    <No>

**简单翻译一下：**

您的一些硬件需要非自由固件文件才能运转。固件可以从可移动介质加载，比如 U 盘或者软盘。

缺失的固件文件是：iwlwifi-5000-5.ucode iwlwifi-5000-4.ucode iwlwifi-5000-3.ucode iwlwifi-5000-2.ucode iwlwifi-5000-1.ucode

如果现在您有可用的介质，请将其插入，然后继续。

从可移动介质加载缺失的固件吗？

<是>    <否>


什么是固件？
*************************

有些硬件除了需要设备驱动程序之外，还要在使用之前加载固件（firmware）或微码（microcode）。这对于网卡（特别是无线网卡）来说很常见，但有些 USB 设备甚至是硬盘控制器也需要加载固件。对于许多显卡，可以在毋须固件的情况下使用基本的功能，但使用高级功能就需要先安装合适的固件到系统中。

有些老的设备需要固件才能工作，这些固件被厂商永久性地置于设备的 EEPROM/Flash 芯片中。现今新设备不再采用这种方式嵌入固件，因此固件必须在系统引导的时候从宿主系统上传到设备中。

根据 Debian GNU/Linux 项目的标准，大多数情况下这些固件属于 non-free，不能被包含在主发行版或安装系统里面。如果设备驱动程序被包含到发行版里面，并且 Debian GNU/Linux 可以合法地发布固件，它通常被单独地放置在仓库的 non-free 区里面。

然而，这并不意味着该硬件不能在安装时使用。从 Debian GNU/Linux 5.0 开始，debian-installer 支持从可移动的介质，比如 U 盘，加载固件或包含固件的软件包。

假如 debian-installer 提示需要固件文件而您又没有该固件，或者不想装非自由的固件到系统上，您可以试着跳过固件加载。有些情况下驱动程序只是在特定情况下提示需要额外的固件，而这个设备在很多系统上可以不使用它就能工作（这通常出现在使用 tg3 驱动的网卡上）。


如何加载缺失的固件
*************************

如上所述，大部分情况下，没有固件设备就无法工作；有时没有固件不会影响基本的功能，但增强功能就无法使用。

如果没有设备驱动程序所需的固件，debian-installer 将显示对话框要求加载缺失的固件。假如选取了选项，debian-installer 将扫描现有设备松散的固件文件或包含固件的软件包。如果搜索到，固件会被复制到正确的位置 ``/lib/firmware`` ，然后加载驱动程序模块。

.. note::

    具体哪些设备会被扫描和支持哪个文件系统，取决于系统的架构、安装方法和安装的阶段。特别是在安装的早期阶段，从 FAT 格式的软盘或 U 盘加载固件更容易成功。在 i386 和 amd64 上固件可以从 MMC 或 SD 卡加载。

假如了解设备毋需固件也可以工作，或者设备在安装时并不需要使用，您可以跳过加载固件。

.. note::

    debian-installer 只提示安装过程中加载的内核模块所需的固件。并不是所有驱动程序都包含在 debian-installer 中，特别是 radeon 没有，因此这意味着一些设备在安装结束时的功能可能与开始时没有什么不同。因此，您的一些硬件可能没有充分发挥其潜力。如果您怀疑是这样，或者只是好奇，那么可以检查 dmesg 命令在新引导的系统上的输出并搜索“固件”名称。


准备介质
=======================

官方的 CD 映像不含商用的固件。这些固件通常是从移动介质，比如 U 盘来加载。另外，包含这些商用固件的非官方的 CD 位于 http://cdimage.debian.org/cdimage/unofficial/non-free/cd-including-firmware/。制作这种 U 盘（或者其他的介质，比如硬盘分区或者软盘）时，固件文件或软件包必须放置在文件系统的根目录或者名为 ``/firmware`` 的目录下。推荐使用 FAT 文件系统，因为在安装过程的早期阶段它肯定能被支持。

大多数固件的压缩包和 zip 文件可以从这里获得： http://cdimage.debian.org/cdimage/unofficial/non-free/firmware/

下载对应版本的压缩包或 zip 文件，然后解压到介质的文件系统里面。

如果您需要的固件没有包含在压缩包里面，可以从档案库（non-free 部分）下载特定的固件。以下概要列出大多数固件软件包，但不保证完整，有些还是非固件软件包： http://packages.debian.org/search?keywords=firmware

也可以复制单独的固件文件到介质。要求不严的固件还可以从已经安装的系统或硬件制造商那里获得。

固件和安装好的系统
============================

任何安装期间加载的固件会自动被复制到安装好的系统。多数情况下这可以保证需要固件的设备可以在系统重新启动之后正常工作。然而，如果安装好的系统运行在与安装程序不同的内核版本，有可能由于版本的偏差造成固件不能加载。

如果固件从一个固件软件包加载，debian-installer 会为安装好的系统也安装该软件包，并自动添加软件包仓库的 non-free 部分到 APT 的 sources.list。这样做的优点在于如果有固件的新版本存在会自动更新。

如果安装过程中跳过加载固件，相关的设备可能无法在安装好的系统中工作，直到手动安装好固件或软件包。

.. note::

    如果固件从不严格的固件文件加载，复制到安装好的系统的固件将不会自动更新，除非对应的固件软件包（如果有的话）在系统安装之后安装。


进入系统之后如何安装固件？
**************************************

如果安装的时候跳过了加载缺失的固件文件，在系统安装完成之后，只需要将固件文件放在 ``/lib/firmware/`` 目录下即可。如果目录不存在，请新建目录 makir /lib/firmware/ 。

.. note::

    ``/lib/firmware/`` 目录不存在也可能是没有安装 firmware。请安装 ``apt-get install firmware-*``。再将固件文件放在 ``/lib/firmware/`` 目录下即可。

参考链接
************************************

* `Debian GNU/Linux 安装手册 <https://www.debian.org/releases/stable/i386/>`_
* `Intel wifi 固件下载 <https://wireless.wiki.kernel.org/en/users/Drivers/iwlwifi>`_
