欢迎来到Lean的Openwrt源码仓库！
=

[English](./README_EN.md)

如何编译自己需要的 OpenWrt 固件
-
注意：
-
1. **不**要用 **root** 用户 git 和编译！！！
2. 国内用户编译前最好准备好梯子
3. 默认登陆IP 192.168.2.1, 密码 无
4. 免责声明：本人不欢迎例如 nobk 这种傻逼使用或者访问本源代码哪怕一个字节，否则一旦他家里因此而发生各种全家富贵的情况，与本人一律无关


编译命令如下:
-
1. 首先装好 Ubuntu 64bit，推荐 Ubuntu 20.04 LTS x64

2. 命令行输入 `sudo apt-get update` ，然后输入

sudo apt-get -y install build-essential asciidoc binutils bzip2 gawk gettext git libncurses5-dev libz-dev patch python3 python2.7 unzip zlib1g-dev lib32gcc1 libc6-dev-i386 subversion flex uglifyjs git-core gcc-multilib p7zip p7zip-full msmtp libssl-dev texinfo libglib2.0-dev xmlto qemu-utils upx libelf-dev autoconf automake libtool autopoint device-tree-compiler g++-multilib antlr3 gperf wget curl swig rsync aria2 ca-certificates python3-pyelftools python-pyelftools

3. 使用 `git clone https://github.com/coolsnowwolf/lede` 命令下载好源代码，然后 `cd lede` 进入目录

4. ```bash
   ./scripts/feeds update -a
   ./scripts/feeds install -a
   make menuconfig
   ```

5. `make -j8 download V=s` 下载dl库（国内请尽量全局科学上网）

6. 输入 `make -j1 V=s` （-j1 后面是线程数。第一次编译推荐用单线程）即可开始编译你要的固件了。

本套代码保证肯定可以编译成功。里面包括了 R21 所有源代码，包括 IPK 的。


二次编译：
```bash
cd lede
git pull
./scripts/feeds update -a && ./scripts/feeds install -a
make defconfig
make -j8 download
make -j$(($(nproc) + 1)) V=s
```

如果需要重新配置：
```bash
rm -rf ./tmp && rm -rf .config
make menuconfig
make -j$(($(nproc) + 1)) V=s
```

编译完成后输出路径：bin/targets

English Version: How to make your Openwrt firmware.
-
Note:
--
1. DO **NOT** USE **ROOT** USER TO CONFIGURE!!!

2. Login IP is 192.168.1.1 and login password is "password".

Let's start!
---
First, you need a computer with a linux system. It's better to use Ubuntu 18 LTS 64-bit.

Next you need gcc, binutils, bzip2, flex, python3.5+, perl, make, find, grep, diff, unzip, gawk, getopt, subversion, libz-dev and libc headers installed.

To install these program, please login root users and type
`
sudo apt-get -y install build-essential asciidoc binutils bzip2 gawk gettext git libncurses5-dev libz-dev patch python3.5 python2.7 unzip zlib1g-dev lib32gcc1 libc6-dev-i386 subversion flex uglifyjs git-core gcc-multilib p7zip p7zip-full msmtp libssl-dev texinfo libglib2.0-dev xmlto qemu-utils upx libelf-dev autoconf automake libtool autopoint device-tree-compiler g++-multilib antlr3 gperf wget swig rsync aria2
`
in terminal

Third, logout of root users. And type this `git clone https://github.com/coolsnowwolf/lede` in terminal to clone this source.

After these please type `cd lede` to cd into the source.

Please Run `./scripts/feeds update -a` to get all the latest package definitions
defined in `feeds.conf` / `feeds.conf.default` respectively
and `./scripts/feeds install -a` to install symlinks of all of them into
`package/feeds/` .

Please use `make menuconfig` to choose your preferred
configuration for the toolchain and firmware.

Use `make menuconfig` to configure your image.

Simply running `make` will build your firmware.
It will download all sources, build the cross-compile toolchain,
the kernel and all chosen applications.

To build your own firmware you need to have access to a Linux, BSD or MacOSX system
(case-sensitive filesystem required). Cygwin will not be supported because of
the lack of case sensitiveness in the file system.

## Note: Addition Lean's private package source code in `./package/lean` directory. Use it under GPL v3.

## GPLv3 is compatible with more licenses than GPLv2: it allows you to make combinations with code that has specific kinds of additional requirements that are not in GPLv3 itself. Section 7 has more information about this, including the list of additional requirements that are permitted.
=======
=======
如果你使用WSL或WSL2进行编译：
------
由于wsl的PATH路径中包含带有空格的Windows路径，有可能会导致编译失败，请在将make -j1 V=s或make -j$(($(nproc) + 1)) V=s改为

首次编译：
```bash
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin make -j1 V=s 
```
二次编译：
```bash
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin make -j$(($(nproc) + 1)) V=s
```

特别提示：
------
1. 源代码中绝不含任何后门和可以监控或者劫持你的 HTTPS 的闭源软件， SSL 安全是互联网最后的壁垒。安全干净才是固件应该做到的；

2. 如有技术问题需要讨论，欢迎加入 QQ 讨论群： OP 共享技术交流群 ,号码 297253733 ，加群链接: 点击链接加入群聊【 OP 共享技术交流群】：[点击加入](https://jq.qq.com/?_wv=1027&k=5yCRuXL "OP共享技术交流群")

3. 想学习 OpenWrt 开发，但是摸不着门道？自学没毅力？基础太差？怕太难学不会？跟着佐大学 OpenWrt 开发入门培训班助你能学有所成
报名地址：[点击报名](http://forgotfun.org/2018/04/openwrt-training-2018.html "报名")

如何为openwrt中的某个模块生成PKG_MIRROR_HASH
------

第一种方法(自动生成):
------
1.在软件包的Makefile中让此项写成这样PKG_MIRROR_HASH:=skip  (如果不加上skip,那么在第三步填充哈希值时会直接删除"PKG_MIRROR_HASH:="这一行)

2.下载软件包(如软件包名为hello,包的Makefile放在package/network/services/hello下)

make package/network/services/hello/download V=s

3.填充哈希值

make package/network/services/hello/check FIXUP=1 V=s

第二种方法(手动生成):
------
1. 首先在软件包的Makefile中让此项空着(PKG_MIRROR_HASH:=)，然后将整个openwrt系统进行编译make -j10

2. 在第一步完成之后会在dl目录下发现一个新模块相关的压缩包，直接使用以下命令来生成哈希值,如:sha256sum 包名.tar.xz
  
3. 将第二步生成的哈希值填充到Makefile的PKG_MIRROR_HASH即可

