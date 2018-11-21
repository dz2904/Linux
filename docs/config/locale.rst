浅析 Linux 的国际化与本地化机制
################################

什么是国际化和本地化
********************************

不同的国家和地区因文化的差异，在日期、时间以及货币符号等表示方式上都不完全相同，最为明显的就是语言。有时在编写软件给用户使用时，开发者、维护者以及最终用户可能分别来自不同的区域，而要求他们均使用同一种语言显然是不明智的，因此当一个程序或者软件编写给全世界人使用时，通常分为两个部分：国际化（internationalization 即缩写为 i18n，这是由于在这个单词的头尾之间包含了 18 个字母）和本地化 （localization，缩写为 l10n）。

国际化，指的是一个程序或软件可给特定的人群使用而无须修改或重新编译源代码。在 ISO C 中，国际化的工作依赖于 locales。程序开发者可使用多样的方式来国际化他们的程序，但是 GNU gettext 已成为了其中的一种标准。

本地化，指的是一个程序或软件在支持国际化的基础上，给定程序特定区域的语言信息使其在信息的输入输出等处理上适应特定区域人群的使用。这里允许程序所使用的一些语言环境变量在程序执行时动态配置。

简单的说，国际化是开发者的任务，是一个一般化的过程；而本地化则是翻译者所做的事情，是一个具体的过程。国际化的运作为本地化工作提供了可能。对于国际化与本地化，有时我们也称为 NLS（Native Language Support）。Glibc（GNU C library）作为 Linux 的 C 标准库为 Linux 处理国际化与本地化提供了基础，Linux 上的程序处理依赖于 glibc（见下图）。

.. image:: ../images/locale.01.jpg

使用和设定系统 locale
********************************

对用户而言，用来控制语言或区域环境生效的功能就叫做 locale。locale 是 glibc 的一个重要组成部分，也是 Linux 国际化和本地化工作的一个重要基础。locale 通过设置一系列的环境变量来满足用户对国际化和本地化的需求。通过 locale 命令，我们不仅可查看到语言环境的当前设置，还可查看当前 locale 可用的名称和字符集。

整个系统使用的 locale 可以通过创建或编辑 ``/etc/locale.conf`` 来配置，或者通过 localectl 设置。

用户可以自定义 locale 配置文件，以实现个性化。用户可以创建自己的 ``$HOME/.config/locale.conf`` 来覆盖系统配置文件。

locale 变量的值有三个要素：语言代码 （Language Code）、 地域代码 （Country Code） 和字符集（Encoding）


.. highlight:: none

::

    [gavin@rudder ~]$ locale
    # 未设置任何 LC_xxx 变量时所使用的默认值 LANG
    LANG=en_US.UTF-8
    # 使用 gettext 翻译的软件会按照先后顺序选择语言
    LANGUAGE=zh_CN:en_GB:en
    # 指定使用某区域的字符分类及处理方式
    LC_CTYPE=zh_CN.UTF-8
    # 指定使用某区域的非货币的数字格式
    LC_NUMERIC="en_US.UTF-8"
    # 指定使用某区域的日期和时间格式
    LC_TIME="en_US.UTF-8"
    # 指定使用某区域的排序规则
    LC_COLLATE="en_US.UTF-8"
    # 指定使用某区域的货币格式
    LC_MONETARY="en_US.UTF-8"
    # 指定使用某区域的响应与信息的格式
    LC_MESSAGES="en_US.UTF-8"
    # 指定使用某区域的纸张大小
    LC_PAPER="en_US.UTF-8"
    # 指定使用某区域的姓名书写方式
    LC_NAME="en_US.UTF-8"
    # 指定使用某区域的地址格式和位置信息
    LC_ADDRESS="en_US.UTF-8"
    # 指定使用某区域的电话号码格式
    LC_TELEPHONE="en_US.UTF-8"
    # 指定使用某区域的度量衡规则
    LC_MEASUREMENT="en_US.UTF-8"
    # 对 locale 自身信息的概述
    LC_IDENTIFICATION="en_US.UTF-8"
    # 用来覆盖掉所有其他 LC_xxx 变量的值
    LC_ALL=

除上边所说的，另有用于指定 locale 可用名称目录的变量 LOCPATH，默认使用的路径是 ``/usr/lib/locale/`` 。另外，当一个程序找寻 locale 环境变量值时，它会依以下优先顺序使用变量。

::

    [1] LANGUAGE
    [2] LC_ALL
    [3] LC_xxx
    [4] LANG

其中 LC_ALL 并不是一个环境变量，它仅是一个可被函数 setlocale （setlocale 的函数原型及其参数 category 的可用值均被定义在头文件 locale.h 中）调用的宏，它的值可覆盖所有其他的 locale 设定 （如果 LC_ALL 的值存在的话，即非空） 。LANG 用于正常指定使用某区域环境值，而 LANGUAGE 则用于指定个人对语言环境值的主次偏好。通常我们会在设定 LANG 后，并通过 LC_xxx 加以修正。

::

    LANGUAGE="en_US:en"
    LANG="en_US.UTF-8"
    LC_CTYPE="zh_CN.UTF-8"

也可以将以上内容添加至 ``/etc/profile`` 或 ``/etc/environment`` 等系统初始文件中，以保证系统在启动后立即使用您所期望的语言环境。值得注意的是，若 locale 被设定为 "C"，那么 LANGUAGE 的值将被忽视。因此我们必须为 LANG （或 LC_ALL） 设置有效的 locale 名称，而非 "C"。

::

    # 当前系统可用的 locale 名称
    [gavin@rudder ~]$ locale – a
    C
    en_AU.utf8
    ...
    POSIX
    zh_CN.utf8
    ...


上边我们看到了两个特别的 locale 名称，C 和 POSIX。当前，POSIX 仅是 C 的一个别名。除 C 和 POSIX 之外，locale 的名称并未标准化。同时，上边可用名称的输出已依 LC_COLLATE 所指定的排序规则进行了排序。另外，我们看到 locale 的名称存在一个命名的格式。

::

    language[_territory[.codeset]][@modifier]


其中 language 是 ISO 639-1 标准中定义的语言代码，territory 是 ISO 3166-1 标准中定义的国家和地区代码，codeset 是字符集的名称（ 如 UTF-8 等 ），而 modifier 则是某些 locale 变体的修正符。若期望使用的 locale 名称未在以上的列表中，那么我们可使用 glibc 提供的命令 localedef 进行添加（命令 localedef 会在相关路径生成必要的数据文件）。

::

    # 通过命令 localedef 添加 fi_FI.UTF-8
    [1] localedef -f UTF-8 -i fi_FI fi_FI.UTF-8
    [2] localedef -f UTF-8 -i fi_FI ./fi_FI


方式 1 将在默认路径上生成一个 locale-archive 文件，而方式 2 则在指定路径产生一个目录，该目录中将包含 locale 相关的数据。另外，命令 localedef 还提供了 --no-archive 选项，该选项可使方式 1 生成的也是一个目录，而非 locale-archive 文件。下面我们通过设置 LC_ALL 和 LC_TIME 的值来了解该 locale 环境变量对时间和日期格式的影响，从而更好的理解 locale 环境变量在系统上的基础作用（如下，另外我们需在运行前确定该 locale 名称是有效的）。

::

    # locale 环境变量对系统命令的影响
    [gavin@rudder ~]$ LC_ALL=en_US.UTF-8 date
    Thu Nov  5 14:13:36 CST 2009

    [gavin@rudder ~]$ LC_TIME=fi_FI.UTF-8 date
    to 5.11.2009 14.13.44 +0800

    [gavin@rudder ~]$ LC_ALL=zh_CN.UTF-8  locale  -ck  LC_TIME
    LC_TIME
    abday="日 ; 一 ; 二 ; 三 ; 四 ; 五 ; 六"
    ...
    ...
    date_fmt="%Y 年 %m 月 %d 日 %A %H:%M:%S %Z"
    time-codeset="UTF-8"

在这里另要指出的是，由 GNU coreutils 提供的 date 命令在实现时加入了以下内容，而这正是 date 命令实现国际化与本地化的关键。

::

    # date 命令的源码片断
    setlocale （LC_ALL, ""）;
    bindtextdomain （PACKAGE, LOCALEDIR）;
    textdomain （PACKAGE）;

字符及字符集处理
********************************

字符以及字符集的处理，是 Linux 国际化与本地化的另一重要内容。在计算机的早期字符集中，每个字符仅使用 6 个、7 个或 8 个位（bits），但这对于类似东方语系的来说是明显不够的，因此出现了双字节字符集甚至更多字节。在字符集编码上有着两个重要的概念，即内码与外码。内码是在计算机内存中使用的编码，而外码则是在计算机外部使用的编码，如存储和传输等。常用的宽字符集内码有 Unicode 和 ISO 10646（也叫做 UCS，即 Universal Character Set）。Unicode 的最初设计是 16 位，而 ISO 10646 使用的是 31 位。经发展这两个标准现几乎没有差异，Unicode 标准对应于 ISO 10646 实现级别 3（即支持所有的 UCS 组合字符）。而我们常用的 UTF-8 即 UCS 变形格式 8，是一种兼容于 ASCII 编码和所有 POSIX 文件系统的可变长编码。

通常，程序要依靠一些分类函数来处理字母、数字及空白等字符，而这些函数是会受到当前 locale 中的 LC_CTYPE 值的影响的。在 ISO C 标准中描述了两种不同的字符处理方式，即是 char 类型和 wchar_t 的宽字符（即 wc）。它们的分类函数分别被定义在头文件 ctype.h 和 wctype.h 中。对于多字节字符串（mbs）以及宽字符串（wcs）的处理函数则被定义在头文件 wchar.h 中。显然，宽字符分类函数更为通用，因为它允许字符集分类的扩展可超过它的可用值。而这在 POSIX 中有描述的字符集扩展已在 glibc 的程序 localedef 中实现。另外，glibc 考虑到 locale 对程序字符的影响，提供了独立于 locale 的更为通用的字符集处理函数及工具 iconv。

::

    # iconv 相关函数
    #include <iconv.h>
    iconv_t iconv_open（const char *tocode,
                      const char *fromcode）;
    int iconv_close（iconv_t cd）;
    size_t iconv（iconv_t cd, char **inbuf, size_t *inbytesleft,
                      char **outbuf, size_t *outbytesleft）;

下面我们通过查看文件系统 /proc 中的内容，以此来观察 Linux 国际化与本地化机制在内存中的情况（如下）。使用命令 cat 查看到的 ``/proc/self/maps`` 文件内容应与命令 locale 返回的系统当前区域环境值相一致（即将下边与第一个相比较）。

::

    # 当前进程的内存映射及访问权限
    [gavin@rudder ~]$ cat /proc/self/maps
    ...
    085a2000-085c3000 rw-p 085a2000 00:00 0          [heap]
    b7a90000-b7acf000 r--p 00000000 08:08 740190     /usr/lib/locale/zh_CN.utf8/LC_CTYPE
    b7acf000-b7ad0000 r--p 00000000 08:08 729171     /usr/lib/locale/en_US.utf8/LC_NUMERIC
    b7ad0000-b7ad1000 r--p 00000000 08:08 781364     /usr/lib/locale/en_US.utf8/LC_TIME
    b7bbd000-b7dbd000 r--p 00000000 08:08 704987     /usr/lib/locale/locale-archive
    b7dbd000-b7dbe000 rw-p b7dbd000 00:00 0
    b7dbe000-b7f1a000 r-xp 00000000 08:08 852866     /lib/tls/i686/cmov/libc-2.9.so
    ...
    b7f27000-b7f2e000 r--s 00000000 08:08 704989     /usr/lib/gconv/gconv-modules.cache
    ...
    b7f32000-b7f4e000 r-xp 00000000 08:08 827406     /lib/ld-2.9.so
    ...


在上边中我们看到了 locale-archive 文件，如上述这个文件等同于其他含 locale 相关数据的目录文件（如 ``/usr/lib/locale/en_US.utf8/LC_TIME`` 等）。文件 gconv-modules.cache 是由命令 iconvconfig 以文件 gconv-modules 生成的 iconv 配置缓存文件，但该文件并不影响 iconv 的使用。当 gconv-modules.cache 不存在时，iconv 将会尝试打开配置文件 gconv-modules。

简述 gettext
********************************

GNU gettext 是为程序实现国际化与本地化而设计的，它作为 GNU 实现软件翻译项目的一个重要部分为程序开发者、翻译者甚至用户提供了一个可生成多语言信息的框架（通过 gettext 提供的一些接口可使程序与系统的语言或区域环境相一致）。由此 gettext 可帮助我们快速的完成程序或软件的国际化与本地化，而 gettext 工具集则帮助我们更好的管理和维护翻译文档，其具体包含了：

* 一组关于如何编写程序来支持信息分类的约定；

* 一个支持信息分类的目录以及文件的命名管理；

* 一个支持获取翻译信息的动态库文件；

* 一些用于管理翻译信息（或已翻译文件）的独立程序；

* 一个支持解析、创建翻译信息的库文件；

* 一个专为 Emacs 提供的模块，方便设置、获取时间戳。

.. image:: ../images/locale.02.jpg

上图为使用 gettext 工具集处理程序国际化和本地化的流程。事实上，在 glibc 中提供了两组不同的接口来完成信息的翻译，但是它们都没被 POSIX 标准所接受。其中一组接口就是 gettext，而另一组接口则是 catgets。虽然 catgets 被定义进了 X/Open 标准中，但这是来着产业的决定，因此它可能并不合理。相比于 gettext 方案，catgets 的不足之处是函数 catgets 的第三个参数（即消息 ID）是唯一的，这将导致程序编写者及翻译者在字符信息管理和维护上的困难，因此建议使用 gettext。下边是一些与 catgets 相关的函数。

::

    # 与 catgets 相关的一些函数
    #include <nl_types.h>
    nl_catd catopen（const char *name, int flag）;
    int catclose（nl_catd catalog）;
    char *catgets（nl_catd catalog, int set_number,
                         int message_number, const char *message）;

一些相关的函数
********************************

在 Linux 实现国际化和本地化时的一个重要函数就是 setlocale()，我们可以在系统工具集（如 date 命令、命令 locale 及 localedef 均有使用此函数）或是一些软件（如文本编辑软件 gedit）的源码中发现其存在的痕迹。

::

    #include <locale.h>
    char *setlocale（int CATEGORY, const char *LOCALE）;

函数 setlocale 被用于设置或查询程序当前的 locale 值。参数 CATEGORY 一共有 13 个可用值（如 LC_COLLATE 等），它们被定义在头文件 locale.h 中，分别表示从 0 至 12 的整数值。第二个参数 LOCALE 应为 locale 名称（如 zh_CN.UTF-8 等），但是仍有两个特殊的值可使用 "" 和 NULL。如果参数 LOCALE 的值是 ""，则函数返回系统当前的环境值（即置程序的环境值与系统相一致）。若 LOCALE 的值为 NULL，则仅返回程序当前 locale 的设置。程序在设置 locale 值之前，其环境值默认是 "C" 或 "POSIX"。对于国际化程序，函数 setlocale 总被调用，同时为了保持程序的通用性，我们一般把 LOCALE 置为 ""。另外，在 清单 6中的另两个函数均被定义在头文件 libintl.h 中。

::

    #include <libintl.h>
    char * textdomain （const char * domainname）;

函数 textdomain 的作用是重设当前的 domain 值以供 gettext() 等函数使用，其参数 domainname 为期望设置的新的 domain 值。如果参数 domainname 为 ""，那么函数将返回默认值 "messages"，但是似乎没人愿意使用该值，因为那样会使程序间出现冲突以至混乱。若 domainname 值为 NULL，则返回程序当前的 domain 值（在先前没有设置的情况下，仍会返回预定值 "messages"）。另外需注意的是，dcgettext() 等自带有参数 domainname 的函数，将不受函数 textdomain 的影响（除非有人把它们的参数 domainname 值设为 NULL）。

::

    #include <libintl.h>
    char * bindtextdomain （const char * domainname, const char * dirname）;
    char * bind_textdomain_codeset （const char * domainname,
    const char * codeset）;


函数 bindtextdomain 的作用是通过给定的 domain 查找路径，可用信息的路径被指定为：

::

    dirname/locale/category/domainname.mo

dirname 即为参数 dirname，若参数 dirname 为 NULL，则函数返回程序当前路径值（默认是 /usr/share/locale），若 dirname 值为 ""，则返回值为空。locale 即为 locale 名称，category 是 locale 的分类，如 LC_MESSAGES 等。domainname 即为参数 domainname。函数 bind_textdomain_codeset 的功能与 bindtextdomain 相近，因此 glibc 在实现时采用了一个内部函数 set_binding_values 并通过对该函数输入参数的控制分别实现以上 2 个函数。

::

    # glibc 中实现函数 bindtextdomain
    /* intl/bindtextdom.c */
    static void
    set_binding_values （domainname, dirnamep, codesetp）
        const char *domainname;
        const char **dirnamep;
        const char **codesetp;
    { ... }

    /* 函数 bindtextdomain */
    char *
    BINDTEXTDOMAIN （domainname, dirname）
        const char *domainname;
        const char *dirname;
    {
     set_binding_values （domainname, &dirname, NULL）;
     return （char *） dirname;
    }

    /* 函数 bind_textdomain_codeset */
    char *
    BIND_TEXTDOMAIN_CODESET （domainname, codeset）
        const char *domainname;
        const char *codeset;
    {
     set_binding_values （domainname, NULL, &codeset）;
     return （char *） codeset;
    }


另外，还有一个可访问 locale 相关信息的重要函数 nl_langinfo，该函数定义于头文件 langinfo.h 中，其作用是通过给定的 item 返回与 locale 相关的信息。

::

    # 函数 nl_langinfo
    #include <langinfo.h>
    char *nl_langinfo（nl_item item）;

    /* locale/nl_langinfo.c */
    char *
    nl_langinfo （item）
        nl_item item;
    {
     return __nl_langinfo_l （item, _NL_CURRENT_LOCALE）;
    }


简单示例
********************************

通过以上的描述我们大致了解了 Linux 对国际化与本地化的支持，下面我们编写一个获取当前系统时间的小程序来更好的理解函数 setlocale 对整个程序的影响。

::

    # 使用了函数 setlocale 的当前系统时间获取程序
    #include <time.h>
    #include <locale.h>
    #include <stdio.h>

    #define SIZE 80

    int main （int argc, char *argv[]）
    {
       time_t now;
       struct tm *timeinfo;
       struct lconv *lc;
       char buffer[SIZE];

       setlocale （LC_ALL, ""）;
       printf （"LC_TIME = %s\n", setlocale （LC_TIME, NULL））;
       printf （"LC_MONETARY = %s\n", setlocale （LC_MONETARY, NULL））;

       time （&now）;
       timeinfo = localtime （&now）;

       strftime （buffer, SIZE, "%c", timeinfo）;
       printf （"Date : %s\n", buffer）;

       lc = localeconv（）;
       printf （"Currency symbol : %s\n", lc->currency_symbol）;

       return 0;
    }

    $ gcc -Wall locale-time.c -o locale-time
    $ LC_ALL=zh_CN.UTF-8 ./locale-time
    LC_TIME = zh_CN.UTF-8
    LC_MONETARY = zh_CN.UTF-8
    Date : 2009 年 11 月 05 日 星期四 19 时 47 分 46 秒
    Currency symbol : ￥


上边，我们不仅展示了 setlocale 函数的 "" 和 NULL 这两个特殊参数值的使用，还使用了 lconv 这个数据结构用于打印与区域相一致的货币符号（lconv 这个有关数字和货币规则信息的结构体及相关函数 localeconv 被定义在头文件 locale.h 中，与 locale 中的 LC_NUMERIC 和 LC_MONETARY 相关）。在程序执行时，我们动态的修改了 locale 环境变量的值以此来更好的观察 setlocale 函数对程序的影响（如前述使用 date 命令时一样，见清单 5）。在没有调用函数 setlocale 的程序中，程序将使用默认环境值 "C" 或 "POSIX"。由于我们在上面提过 glibc 提供了两组不同的接口实现程序的国际化与本地化，为了更好的理解这两种方式，我们在下面分别进行展示。

::

    # gettext 使用示例
    #include <locale.h>
    #include <libintl.h>
    #include <stdio.h>

    #define PACKAGE "gettext-hello"
    #define LOCALEDIR "po"
    #define N_（msgid） gettext（msgid）

    int main （int argc, char *argv[]）
    {
       setlocale （LC_CTYPE, "zh_CN.UTF-8"）;
       setlocale （LC_MESSAGES, "zh_CN.UTF-8"）;

       bindtextdomain （PACKAGE, LOCALEDIR）;
       textdomain （PACKAGE）;

       /* Translators: 这里仅是一个注释 */
       printf （N_（"Are you ok?\n"））;

       return 0;
    }


我们指定使用 locale 名字为 "zh_CN.UTF-8" 以此方便我们建立目录进行测试，但是更通常的做法是使用 "" 作为参数值使程序适应不同的语言（区域）环境。另外，我们需要为这个简单的程序做一个翻译，并生成一个可用的二进制翻译文件。我们使用由 GNU gettext 提供的工具 xgettext 及 msgfmt 来完成翻译档的生成，但这仅是用于制作、维护 PO（Portable Object）和 MO（Machine Object）文件的部分工具集。

::

    # 执行 gettext 示例程序
    $ xgettext --add-comments  --keyword=N_ gettext-hello.c -o \
     > gettext-hello.pot --from-code=UTF-8
     $ cp gettext-hello.pot gettext-hello.po
     $ cat locale-hello.po
     ...
    "Content-Type: text/plain; charset=UTF-8\n"
    "Content-Transfer-Encoding: 8bit\n"
     ...
     #. Translators: 这里仅是一个注释
     #: locale-hello.c:23
     msgid "Are you ok?\n"
     msgstr "你还好吗？ \n"

     $ mkdir -p po/zh_CN.UTF-8/LC_MESSAGES/
     $ msgfmt gettext-hello.po -o gettext-hello.mo
     $ mv gettext-hello.mo po/zh_CN.UTF-8/LC_MESSAGES/
     $ unset LANGUAGE
     $ gcc -Wall gettext-hello.c -o gettext-hello
     $ LC_ALL=zh_CN.UTF-8 ./ gettext-hello
    你还好吗？


::

    # catgets 使用示例
    #include <nl_types.h>
    #include <locale.h>
    #include <stdio.h>

    #define CATALOG_NAME "catgets-hello.cat"

    int main （int argc, char *argv[]）
    {
       nl_catd catd;
       setlocale （LC_ALL, ""）;
       printf （"LC_MESSAGES = %s\n", setlocale （LC_MESSAGES, NULL））;

       catd = catopen （CATALOG_NAME, NL_CAT_LOCALE）;
       if（catd == （nl_catd） -1） {
           perror（"catopen"）;
           return 1;
       }

       int set_no=11;
       int msg_id=14;
       printf（"%s\n", catgets （catd, set_no, msg_id, "Are you OK?"））;

       if（catclose（catd） < 0） {
           perror （"catclose"）;
           return 1;
       }

       return 0;
    }


我们在 catgets 示例代码中加上了错误处理，但这仅是为了更好的展示。通常情况下这是不需要的，因为我们应尽量使程序运行下去而不是中断。在编辑完程序所需的翻译档后，我们执行这个简单的 catgets 示例。

::

    # 执行 catgets 示例程序
    $ cat catgets-hello.msg
     ...
     $set 11
     14 你还好吗？
     15 I am fine,thanks.
     ...

     $ gencat catgets-hello.msg -o catgets-hello.cat
     $ mv catgets-hello.cat  po/zh_CN.UTF-8/LC_MESSAGES/
     $ gcc -Wall catgets-hello.c -o catgets-hello
     $ export NLSPATH=po/%L/LC_MESSAGES/%N
     $ LC_ALL=zh_CN.UTF-8 ./ catgets-hello
     LC_MESSAGES = zh_CN.UTF-8
    你还好吗？


我们使用命令 gencat 生成程序所需的二进制翻译档并通过 NLSPATH 指定该文件存放的位置。若不指定该变量，默认的位置将是如下所示。

::

    [1] prefix/share/locale/%L/%N
    [2] prefix/share/locale/%L/LC_MESSAGES/%N


其中 ``%L`` 用于指定 locale 名字，``%N`` 为文件名。对于上面几个示例及命令，更好的执行方式是通过 strace 等调试工具进行跟进，这不仅能熟悉相关函数的作用，并可更好的理解 Linux 国际化与本地化机制。

结束语
********************************

我们从国际化与本地化的概念深入到 Linux 实现国际化与本地化机制的 glibc 源码，并尝试从多个角度理解 Linux 上的国际化与本地化机制的运作，最后我们还编写了一些示例，但仍有一些内容被我们忽略，如多语言化、X Window 系统的国际化环境等。另外，一个值得关注的项目是 uClibc，它是一个为嵌入式 Linux 设计的 C 库，相比于 glibc 小巧许多但功能及实现上稍有差异。

扩展阅读：
==============================

* `查阅“glibc 手册”，获取更多 glibc 的知识。 <https://www.gnu.org/software/libc/manual/>`_

* `查阅“gettext 工具集手册”，了解有关 GNU gettext 工具集的内容。  <https://www.gnu.org/software/gettext/manual/gettext.html>`_

* `查看文章“Locale tutorial”，了解关于 locale 和 catgets 的内容。  <http://www.kulichki.com/moshkow/CYRILLIC/locale-tutorial-0_8.txt>`_

* `查阅“UTF-8 and Unicode FAQ for Unix/Linux”文档，了解更多有关字符集的知识。  <http://www.cl.cam.ac.uk/~mgk25/unicode.html>`_

* `How to change system locale on RHEL7? <https://access.redhat.com/solutions/974273>`_
