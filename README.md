编译命令如下:
-
1. 首先装好 Ubuntu 64bit，推荐  Ubuntu  18 LTS x64

2. 命令行输入 `sudo apt-get update` ，然后输入
`
sudo apt-get -y install build-essential asciidoc binutils bzip2 curl gawk gettext git libncurses5-dev libz-dev patch python3.5 python2.7 unzip zlib1g-dev lib32gcc1 libc6-dev-i386 subversion flex uglifyjs git-core gcc-multilib p7zip p7zip-full msmtp libssl-dev texinfo libglib2.0-dev xmlto qemu-utils upx libelf-dev autoconf automake libtool autopoint device-tree-compiler g++-multilib antlr3 gperf
`

3. `git clone -b 19.07 --single-branch https://github.com/Lienol/openwrt openwrt19` 命令下载好源代码，然后 `cd openwrt19` 进入目录

4. ```bash
   ./scripts/feeds clean
   ./scripts/feeds update -a
   ./scripts/feeds install -a
   make menuconfig
   ```

5. `make -j8 download V=s` 下载dl库（国内请尽量全局科学上网）

6. 输入 `make -j1 V=s` （-j1 后面是线程数。第一次编译推荐用单线程）即可开始编译你要的固件了。

7. 编译完成后输出路径：openwrt19/bin/targets

你可以自由使用，但源码编译二次发布请注明我的 GitHub 仓库链接。谢谢合作！

 -----------------------------------------------分割线-----------------------------------------------
 
以下内容仅供自己注释使用，上述内容为作者Lienol(Li大，又名大雕)的原文中文说明，英文部分在此副本已删除，望知悉。
-

As the author has removed the part of the original repository on plugins about some add-on, so if want to compiling completed contents,
need to add ```src-git lienol https://github.com/harry3633/openwrt-package``` to ```./feeds.conf.default```. Thank you:)

注意事项：
-

1.本源码要求以非root身份去执行，即上文的sudo是不要不去执行的，但考虑部分伺服器开头即给的是root身份，可参考以下执行：

（以下内容需用最高权限执行，不规范之处，请多多包涵）

```
useradd speleon
chmod 777 /etc/sudoers
vim /etc/sudoers   ------>  speleon ALL=(ALL:ALL) ALL
chmod 440 /etc/sudoers
mkdir /home/speleon
chmod 755 /home/speleon
chown speleon /home/speleon
passwd
passwd speleon
su speleon
cd ~
```

2.部分参考的来源数据

```
src-git packages https://github.com/Lienol/openwrt-packages.git;19.07
src-git routing https://git.openwrt.org/feed/routing.git;openwrt-19.07
src-git telephony https://git.openwrt.org/feed/telephony.git;openwrt-19.07
src-git luci https://github.com/Lienol/openwrt-luci.git;17.01
src-git diy1 https://github.com/xiaorouji/openwrt-package.git;master

src-git kenzo https://github.com/kenzok8/openwrt-packages
src-git lienol https://github.com/fw876/helloworld
```

2.1参考链接

```
https://mianao.info/2020/05/05/%E7%BC%96%E8%AF%91%E6%9B%B4%E6%96%B0OpenWrt-PassWall%E5%92%8CSSR-plus%E6%8F%92%E4%BB%B6
```

3.自己选用的插件（遵循上述内容）
在```make menuconfig```里的LuCI-->Applications里

```
luci_app_p*******
         privoxy（ 放弃，不会用:) ）
         minidlna
         kcptun( 伺服器不支持 )
         frpc
         qos（ 有相似功能的，之后自己的编译去除了 ）
```

4.源码中.config默认已添加对IPv6的支持，如需使用，在网络-->DHCP中的IPv6设置内三个都选中继模式，该部分已测试通过，未发现问题。

详情请参考：

```
https://blog.csdn.net/fjh1997/article/details/107507648 
#IPv6的直接配置文件内容，测试已通过，其中network毋须按文章修改，用自身原有的即可。
```

5.编译问题

由于该份源码极为复杂，且需要自身网络支持，考虑经济等因素，故选用了*某某家的美国虚拟伺服器*，推荐选择按时计费的，参考配置：1v Core CPU, 2048MB RAM，充值之后可多次编译且资费极低。
由于临时租用，更适合全新编译，并采用单线程以保证不过度超负荷使用,大约需要四个小时，编译完成即可通过SFTP将自己压缩好后的targets传至本机。另ubuntu 18可选ubuntu 18.04 LTS。

当然，还有一种就是借用GitHub自身的Action服务通过config让GitHub的服务器全自动编译对应自己架构的包，免费，不过目前没有试过了:)。
P.S.:x86_64的在Telegram上有人自己编译，请自行寻找。

6.make menuconfig

这里会进入一个彩色界面，用于定制编译属于自己的OpenWRT，其中前三项涉及设备模块选择以及架构，不能选错，详细内容可在面板中的状态-->概况找到。

在此处以本人用的NewWifi3 D2为例：

```
        Target:    MediaTek Ralink MIPS
     Subtarget:    MT7621 base boards
Target Profile:    Newwifi D2
```

其余的主要就是在LuCI-->Applications当中找到适合自己的插件，没有推荐。
其它选项主要是核心模块选择，涉及设备特性，需硬件自身支持。
可以参考：```https://jingyan.baidu.com/article/cdddd41c948b4b53cb00e180.html```

本源码fork自Lienol的源码，除本说明外，未改动其他内容，已在2020年10月22日通过编译安装sysupgrade包在NewWifi3 D2上已成功使用。
-
友情提示：如果只是添加插件，可保留配置在面板升级，否则为保证不出错，请去除保留配置一项。若出错无法找到，可重新不保留配置刷入固件测试，再考虑发issue给Lienol。
         如果非同一固件或固件（可能）变化较大，建议进入不死breed刷入固件，备份！！！（通过这种方式升级为不保留配置）

该份源码官方最后修改自10日前，于24日拷贝。
-

除了遵循原作者的许可协议之外，另在此附：本代码仅供参考学习，且不提供支持，若同为上述设备，可通过Telegram联系@speleon_kishi。（附加的说明仅适用于该拷贝的副本）
-

自即日起，本副本视为已归档。
-

升级前务必备份固件和配置！！！备份！！！备份！！！
-
