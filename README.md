# Try-for-V2Ray

个人使用向的，且经过各项测试的V2ray翻墙记录，应该会逐渐包含自己的主页同步，代理和渗透等，如果没有咕咕咕的话(划掉)<br>

## 1.服务器选择-Server selection(๑•̀ㅂ•́)و✧

尝试一下BandwagonHost的，应该是更稳定且更优化的，但是奈何太贵了，个人支付不来，于是只能转回Vultr了（贫穷限制了我的……QAQ）<br>

先埋伏它一手，改网址把try50的优惠改为try100的优惠，嫖一个100刀的优惠券，啊？要充值10刀才能使用啊，还得用卡不能支付宝，啧，学坏了<br>

不过不用担心，我们有海外留学的胖友可以……<br>

这样，第一步【拥有一个海外云服务器】的目标就达成了（推荐整个好一点的ip）(๑•̀ㅂ•́)و✧<br>

## 2.V2Ray搭建-V2Ray Setup(°ー°〃)
### 2.1 链入VPS服务器
可以使用XShell，也推荐使用XShell，但是我图方便就直接PUTTY链入做最基本的操作了，之后重写主页上去应该还是要XShell的<br>

我们就一直以root权限进行操作了，更方便一点<br>

### 2.2 时间校准与防火墙关闭
这个只是防止出现问题的两步操作，首先是时间校准：<br>
```linux
data -R --查看VPS服务器所在时区时间，记得自己换算一下对不对
date --set="20XX-XX-XX XX:XX:XX" --如果时间不对，时间修改命令
```

然后是防火墙关闭，都是基础的Linux操作：<br>
```Linux
systemctl status firewalld --查看当前防火墙状态。 
systemctl stop firewalld --关闭当前防火墙。 
systemctl disable firewalld --开机防火墙不启动。
```

### 2.3 脚本搭建V2Ray初步环境
该脚本由 V2Ray 官方提供。该脚本仅可以在 Debian 系列或者支持 Systemd 的 Linux 操作系统使用。<br>

除非你是大佬，或者能够自行处理类似 command not found 的问题，否则请你使用 CentOS 8.x 以上或者 Ubuntu 16.04 以上的 Linux 系统。 本文默认使用 CentOS 8 系统作为示范。<br>
```Linux
--下载脚本并安装
source <(curl -sL https://multi.netlify.app/v2ray.sh) --zh
复制生成的vmess链接
```

### 2.4 更新说明
在VPS端再次执行安装脚本的命令就可以了，V2Ray的更新还算快的，之后使用的客户端也是，core更新频率较快，请自行考虑更新的频率，推荐正式版本（.0版本）<br>
```Linux
--更新版本
source <(curl -sL https://multi.netlify.app/v2ray.sh) -k
```
## 3.V2RayN的配置与使用-Configuration and use of v2rayn o(￣ヘ￣o＃)
### 3.1 获取V2RayN客户端
V2RayN是非常适配V2ray的桌面化应用程序，有图形界面不用用命令，是█████吗？<br>
下载地址：https://github.com/2dust/v2rayN/releases/latest <br>
下载V2RayN-Core.zip，然后解压<br>

### 3.2 配置V2RayN
下载V2Ray的内核<br>
下载地址：https://github.com/v2ray/v2ray-core/releases <br>
然后将 v2rayN 目录下所有文件复制到v2ray-windows-64解压后的目录，即两个下载好的文件需要在同一目录。重复文件跳过别替换<br>

### 3.3 配置V2RayN
客户端配置，直接添加Vmess服务器，选择从剪贴板导入之前生成的vmess链接<br>
在缩略客户端图标右键，选择导入的服务器并且开启代理即可翻墙了（大概）<br>

## 4.BBR加速-BBR acceleration ┑(￣Д ￣)┍
仅CentOS环境下的BBR加速使用，其他环境下脚本不同，而且也有不需要加速而自行更换了内核的例子，没错，就是某Debian<br>
```Linux
wget "https://github.com/cx9208/bbrplus/raw/master/ok_bbrplus_centos.sh" && chmod +x ok_bbrplus_centos.sh && ./ok_bbrplus_centos.sh
```
安装和替换需要点时间<br>
以上过程要求重启系统，输入“Y”回车即可了。<br>
安装后，执行uname -r，显示4.9以上内核版本则切换内核成功<br>
执行lsmod | grep bbr，显示有tcp_bbr则开启成功<br>

## 5.先写到这里了，图都没配，有时间再更新tls之流（应该不要多久）
