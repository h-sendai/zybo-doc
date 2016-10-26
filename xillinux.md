Xillinuxのセットアップなど
==========================

参考URL:

<http://xillybus.com/xillinux>
<http://qiita.com/kazuyamashi/items/b49df82dc8d868364d53>

[xillybus host programming guide linux (PDF)]
(http://xillybus.com/downloads/doc/xillybus_host_programming_guide_linux.pdf)

<http://xillybus.com/xillinux>
から
<http://xillybus.com/downloads/xillinux-eval-zybo-1.3c.zip>
および
<http://xillybus.com/downloads/xillinux-1.3.img.gz>
をダウンロードする。

xillinux-1.3.img.gzをgzip -dで伸長してmicroSDカードにddで書く。

xillinux-eval-zybo-1.3c.zipを展開してxillydemo.bitを作る。
vivadoが必要。作りかたは
<http://xillybus.com/downloads/doc/xillybus_getting_started_zynq.pdf>
のGetting started guide for zynqに書いてある。
vivadoを起動して
run tcp script...
メニューで
xillinux-eval-zybo-1.3c/verilog/xillydemo-vivado.tcl
を選んで走らせる。tcp consoleタブでProject created: xillydemo
とでればOK。
そのあと左側メニューからGenerate Bitstreamをクリックする。
けっこう時間がかかって
xillinux-eval-zybo-1.3c/verilog/vivado/xillydemo.runs/impl_1/xillydemo.bit
ができる。xillydemo.bitが必要なファイル。

microSDカードをLinuxでmountする。


    mount /dev/sdb1 /mnt
    ls /mnt

するとuImageファイルがある。ここにxillydemo.bitと
xillinux-eval-zybo-1.3c/bootfiles/boot.bin
xillinux-eval-zybo-1.3c/bootfiles/devicetree.dtb
ファイルをコピーする。/mntには都合

    uImage
    boot.bin
    devicetree.dtb
    xillydemo.bit

の4つのファイルがある状態になる。

microSDカードをzyboにさしてブートする。
シリアルコンソールの他、VGAにも出力され、autologinでrootで自動ログイン
している状態で起動する。
VGAは文字端末の状態になっている。GUIを起動するには
startx
と入力する。
zyboにはUSBポートが1つしかないのでUSBキーボード、USBマウスを同時に使う
にはUSBハブが必要になる。抜き差しして、交互に使うということは可能。

OS関連はubuntuなのでネットワークの設定は/etc/network/interfacesファイルで行う。
static IP addressならloに追加して

    # interfaces(5) file used by ifup(8) and ifdown(8)
    auto lo
    iface lo inet loopback
    #
    auto eth0
    iface eth0 inet static
    address 192.168.1.123
    netmask 255.255.255.0
    broadcast 192.168.1.255
    # gateway 192.168.1.20
    # dns-nameservers 130.87.232.2
    # dns-search kek.jp

と書く。

    root@localhost:/etc/apt# uname -a
    Linux localhost.localdomain 3.12.0-xillinux-1.3 #1 SMP PREEMPT Thu Mar 13 18:39:32 IST 2014 armv7l armv7l armv7l GNU/Linux


    root@localhost:/etc/apt# gcc -v
    Using built-in specs.
    COLLECT_GCC=gcc
    COLLECT_LTO_WRAPPER=/usr/lib/gcc/arm-linux-gnueabihf/4.6/lto-wrapper
    Target: arm-linux-gnueabihf
    Configured with: ../src/configure -v --with-pkgversion='Ubuntu/Linaro 4.6.3-1ubuntu5' --with-bugurl=file:///usr/share/doc/gcc-4.6/README.Bugs --enable-languages=c,c++,fortran,objc,obj-c++ --prefix=/usr --program-suffix=-4.6 --enable-shared --enable-linker-build-id --with-system-zlib --libexecdir=/usr/lib --without-included-gettext --enable-threads=posix --with-gxx-include-dir=/usr/include/c++/4.6 --libdir=/usr/lib --enable-nls --with-sysroot=/ --enable-clocale=gnu --enable-libstdcxx-debug --enable-libstdcxx-time=yes --enable-gnu-unique-object --enable-plugin --enable-objc-gc --enable-multilib --disable-sjlj-exceptions --with-arch=armv7-a --with-float=hard --with-fpu=vfpv3-d16 --with-mode=thumb --disable-werror --enable-checking=release --build=arm-linux-gnueabihf --host=arm-linux-gnueabihf --target=arm-linux-gnueabihf
    Thread model: posix
    gcc version 4.6.3 (Ubuntu/Linaro 4.6.3-1ubuntu5) 

    root@localhost:/usr/bin# /lib/arm-linux-gnueabihf/libc.so.6 
    GNU C Library (Ubuntu EGLIBC 2.15-0ubuntu10) stable release version 2.15, by Roland McGrath et al.
    Copyright (C) 2012 Free Software Foundation, Inc.
    This is free software; see the source for copying conditions.
    There is NO warranty; not even for MERCHANTABILITY or FITNESS FOR A
    PARTICULAR PURPOSE.
    Compiled by GNU CC version 4.6.3.
    Compiled on a Linux 3.2.14 system on 2012-04-19.
    Available extensions:
        crypt add-on version 2.1 by Michael Glad and others
        GNU Libidn by Simon Josefsson
        Native POSIX Threads Library by Ulrich Drepper et al
        Support for some architectures added on, not maintained in glibc core.
        BIND-8.2.3-T5B
    libc ABIs: UNIQUE
    For bug reporting instructions, please see:
    <http://www.debian.org/Bugs/>.

    root@localhost:/usr/bin# perl -v

    This is perl 5, version 14, subversion 2 (v5.14.2) built for arm-linux-gnueabihf-thread-multi-64int
    (with 53 registered patches, see perl -V for more detail)

    Copyright 1987-2011, Larry Wall

    Perl may be copied only under the terms of either the Artistic License or the
    GNU General Public License, which may be found in the Perl 5 source kit.

    Complete documentation for Perl, including FAQ lists, should be found on
    this system using "man perl" or "perldoc perl".  If you have access to the
    Internet, point your browser at http://www.perl.org/, the Perl Home Page.

    root@localhost:/usr/bin# python
    Python 2.7.3 (default, Apr 21 2012, 01:05:55) 
    [GCC 4.6.3] on linux2
    Type "help", "copyright", "credits" or "license" for more information.
    >>> 

    root@localhost:~# ifconfig eth0
    eth0      Link encap:Ethernet  HWaddr 00:0a:35:00:01:22  
          inet addr:192.168.1.123  Bcast:192.168.1.255  Mask:255.255.255.0
          inet6 addr: fe80::20a:35ff:fe00:122/64 Scope:Link
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:0 errors:0 dropped:0 overruns:0 frame:0
          TX packets:37 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000 
          RX bytes:0 (0.0 B)  TX bytes:7548 (7.5 KB)
          Interrupt:54 Base address:0xb000 

<http://keitetsu.blogspot.jp/2015/01/zyboubuntu.html>
などをみるとubuntu 14.04もうごいているみたい。

rootでのautologinは
/etc/init/tty[1-6].confとttyPS0.conf (console)で指定されている:

    start on stopped rc RUNLEVEL=[2345] and (
            not-container or
            container CONTAINER=lxc or
            container CONTAINER=lxc-libvirt)

    stop on runlevel [!2345]

    respawn
    exec /sbin/getty -8 -a root 115200 ttyPS0 

-a rootの部分がautologinユーザーを示す。

別ubuntu 12.04マシンのautologinなしのtty1.confは以下のとおりに
なっている。

    start on stopped rc RUNLEVEL=[2345] and (
            not-container or
            container CONTAINER=lxc or
            container CONTAINER=lxc-libvirt)

    stop on runlevel [!2345]
    respawn
    exec /sbin/getty -8 38400 tty1

インターネットに接続する環境を用意するとapt-getなどできるようになる。

    apt-get install openssh-server

しておくとsshでアクセスできるようになる。

rootのパスワードは付いていないのでconsoleからautologinした状態で
passwdコマンドでパスワードを付けると他PCから
ssh root@192.168.1.123
でログインできるようになる。

/root/xillybus/demoapps

が用意されているのでここでmakeすると

    fifo
    memread
    memwrite
    streamread
    streamwrite

ができる。/dev/以下には

    /dev/xillybus_audio
    /dev/xillybus_mem_8
    /dev/xillybus_read_32
    /dev/xillybus_read_8
    /dev/xillybus_smb
    /dev/xillybus_write_32
    /dev/xillybus_write_8

というデバイスファイルができている。いまはFPGAの回路が入っていない
のですどおしになる。
端末を2個開いて

    ./streamread /dev/xillybus_read_32 (32bitでリード)
してもう片方の端末で

    ./streamwrite /dev/xillybus_write_32

すると端末読みだし状態になる。適当にキーボードから
入力するとstreamreadした端末に入力が表示される。
ARM -> FIFO -> FPGA回路 -> FIFOと流れているらしい。
32ビット読みだしということでキーを4回押すとstreamreadしているほうで
入力が表示される。

clock_gettime()での時刻の取得
何回もclock_gettime()させて時刻をプリントさせてみると、
clock_gettime()のたびに進んでいる時刻情報がとれるわけではない
ことがわかった。
時刻情報がアップデートされるのは18904-18905ナノ秒(18.9マイクロ秒)
おきのようで、この間clock_gettime()は29〜30回まわる。

ROOTのコンパイル

    g++  -Wall -fsigned-char -fPIC -Iinclude    -pthread -o cint/cintex/src/CINTFunctional.o -c /home/sendai/src    /root/cint/cintex/src/CINTFunctional.cxx
    /tmp/ccoBuHHh.s: Assembler messages:
    /tmp/ccoBuHHh.s:5004: Error: r13 not allowed here -- `sub sp,fp,#4'
    /tmp/ccoBuHHh.s:5040: Error: r13 not allowed here -- `sub sp,fp,#4'
    make: *** [cint/cintex/src/CINTFunctional.o] Error 1

でとまる。
<https://root.cern.ch/phpBB3/viewtopic.php?t=19078>
に同様な話がある。
cintexは必要なのか? 必要ないならconfigure --disable-cintexでコンパイル
しないようにすることもできると書いてある。

