# Try-for-V2Ray

个人使用向的，且经过各项测试的V2ray翻墙记录，应该会逐渐包含自己的主页同步，代理和渗透等，如果没有咕咕咕的话(划掉)<br>

## 1.服务器选择-Server selection(๑•̀ㅂ•́)و✧

尝试一下BandwagonHost的，应该是更稳定且更优化的，但是奈何太贵了，个人支付不来，于是只能转回Vultr了（贫穷限制了我的……QAQ）<br>

先埋伏它一手，改网址把try50的优惠改为try100的优惠，嫖一个100刀的优惠券，啊？要充值10刀才能使用啊，还得用卡不能支付宝，啧，学坏了<br>

不过不用担心，我们有海外留学的胖友可以……<br>

这样，第一步【拥有一个海外云服务器】的目标就达成了（推荐整个好一点的ip）(๑•̀ㅂ•́)و✧<br>

## 2.V2Ray搭建-V2Ray Setup(°ー°〃)
### 2.1 链入VPS服务器
可以使用XShell，也推荐使用XShell，之后重写主页上去应该还是要XShell的<br>

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
--启动v2ray服务
systemctl start v2ray
--重启v2ray服务
systemctl restart v2ray
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

## 5.Mux多路复用-Mux（Multiplexing） (￢_￢)
Mux 实质上不能提高网速，但对并发连接比较有效，如浏览图片较多的网页;使用 Mux 看视频、下载或者测速通常都有反效果。（我是不开启的，请各位按自己需求来）<br>
Mux 只需要在客户端启用，服务器端自动适配。
```Json
"mux": {
        "enabled": false,
        "concurrency": -1
      }
```
更改为：
```Json
"mux": {
        "enabled": true,
        "concurrency": 8
      }
```
concurrency: number,最大并发连接数。最小值1，最大值1024，缺省默认值8。<br>
特殊值-1，不加载mux模块。(4.22.0+)<br>
这个数值表示了一个 TCP 连接上最多承载的 Mux 连接数量。当客户端发出了 8 个 TCP 请求，而concurrency=8时，V2Ray 只会发出一条实际的 TCP 连接，客户端的 8 个请求全部由这个 TCP 连接传输。<br>

## 6.Kcp既mkcp的实现-Kcp/mkcp (:3[▓▓] 
mKCP 使用 UDP 来模拟 TCP 连接，请确定主机上的防火墙配置正确。mKCP 牺牲带宽来降低延迟。传输同样的内容，mKCP 一般比 TCP 消耗更多的流量。<br>
但其实脚本已经默认做了kcp的搭建，不需要再多余的操作了，下面只是对协议各项参数的介绍和可选项解释<br>
```Json
"streamSettings": {
        "network": "kcp",
        "kcpSettings": {
          "mtu": 1350,
          "tti": 50,
          "uplinkCapacity": 12,
          "downlinkCapacity": 100,
          "congestion": false,
          "readBufferSize": 2,
          "writeBufferSize": 2,
          "header": {
            "type": "wechat-video"
          }
        }
      },
```
### 注意这里修改值的话服务器端与客户端都要在config.json文件内修改并且在V2RayN的配置中进行相应修改并重启v2ray服务
mtu: number,最大传输单元（maximum transmission unit），请选择一个介于 576 - 1460 之间的值。默认值为 1350。<br>
tti: number,传输时间间隔（transmission time interval），单位毫秒（ms），mKCP 将以这个时间频率发送数据。请选译一个介于 10 - 100 之间的值。默认值为 50。<br>

uplinkCapacity: number,上行链路容量，即主机发出数据所用的最大带宽，单位 MB/s，默认值 5。<br>
注意是 Byte 而非 bit。可以设置为 0，表示一个非常小的带宽。<br>
downlinkCapacity: number,下行链路容量，即主机接收数据所用的最大带宽，单位 MB/s，默认值 20。<br>
注意是 Byte 而非 bit。可以设置为 0，表示一个非常小的带宽。<br>
uplinkCapacity 和 downlinkCapacity 决定了 mKCP 的传输速度。以客户端发送数据为例，客户端的 uplinkCapacity 指定了发送数据的速度，而服务器端的 downlinkCapacity 指定了接收数据的速度。两者的值以较小的一个为准。<br>
推荐把 downlinkCapacity 设置为一个较大的值，比如 100，而 uplinkCapacity 设为实际的网络速度。当速度不够时，可以逐渐增加 uplinkCapacity 的值，直到带宽的两倍左右。<br>

congestion: true | false,是否启用拥塞控制，默认值为 false。<br>
开启拥塞控制之后，V2Ray 会自动监测网络质量，当丢包严重时，会自动降低吞吐量；当网络畅通时，也会适当增加吞吐量。<br>

readBufferSize: number,单个连接的读取缓冲区大小，单位是 MB。默认值为 2。<br>
writeBufferSize: number,单个连接的写入缓冲区大小，单位是 MB。默认值为 2。<br>

readBufferSize 和 writeBufferSize 指定了单个连接所使用的内存大小。<br>
在需要高速传输时，指定较大的 readBufferSize 和 writeBufferSize 会在一定程度上提高速度，但也会使用更多的内存。<br>
在网速不超过 20MB/s 时，默认值 1MB 可以满足需求；超过之后，可以适当增加 readBufferSize 和 writeBufferSize 的值，然后手动平衡速度和内存的关系。<br>

header: HeaderObject,数据包头部伪装设置<br>
```Json
HeaderObject
{
  "type": "none"
}
```
type: string,伪装类型，可选的值有：<br>
"none": 默认值，不进行伪装，发送的数据是没有特征的数据包。<br>
"srtp": 伪装成 SRTP 数据包，会被识别为视频通话数据（如 FaceTime）。<br>
"utp": 伪装成 uTP 数据包，会被识别为 BT 下载数据。<br>
"wechat-video": 伪装成微信视频通话的数据包。<br>
"dtls": 伪装成 DTLS 1.2 数据包。<br>
"wireguard": 伪装成 WireGuard 数据包。(并不是真正的 WireGuard 协议)<br>

## 7.回过头来解释Vmess协议-Vmess (¦3[▓▓]
VMess 是一个加密传输协议，它分为入站和出站两部分，通常作为 V2Ray 客户端和服务器之间的桥梁。<br>
（我不建议使用Shadowsocks协议，尽管他会更快）<br>
VMess 依赖于系统时间，请确保使用 V2Ray 的系统 UTC 时间误差在 90 秒之内，时区无关。在 Linux 系统中可以安装ntp服务来自动同步系统时间。<br>
VMess 的配置分为两部分，InboundConfigurationObject和OutboundConfigurationObject，分别对应入站和出站协议配置中的settings项。<br>
因为默认导出就是Vmess协议，我这里就直接以客户端出站设置来讲参数和可选项解释<br>
```Json
 "outbounds": [
    {
      "tag": "proxy",
      "protocol": "vmess",
      "settings": {
        "vnext": [
          {
            "address": "境外vpsIP",
            "port": 自己设定的端口号,
            "users": [
              {
                "id": "自主生成合法id",
                "alterId": 0,
                "email": "t@t.tt",
                "security": "auto"
              }
            ]
          }
        ]
      },
```
### 注意这里修改值的话服务器端与客户端都要在config.json文件内修改并且在V2RayN的配置中进行相应修改并重启v2ray服务
alterId：number，为了进一步防止被探测，一个用户可以在主 ID 的基础上，再额外生成多个 ID。<br>
这里只需要指定额外的 ID 的数量，推荐值为 4。<br>
不指定的话，默认值是 0。最大值 65535。这个值不能超过服务器端所指定的值。<br>

email: string,用户邮箱地址，用于区分不同用户的流量。<br>
"email": "t@t.tt",为了做流量统计加入的虚假email,t域名最短被选用<br>

security: "aes-128-gcm" | "chacha20-poly1305" | "auto" | "none",加密方式，客户端将使用配置的加密方式发送数据，服务器端自动识别，无需配置。<br>
"aes-128-gcm"：推荐在 PC 上使用<br>
"chacha20-poly1305"：推荐在手机端使用<br>
"auto"：默认值，自动选择（运行框架为 AMD64、ARM64 或 s390x 时为aes-128-gcm加密方式，其他情况则为 Chacha20-Poly1305 加密方式）<br>
"none"：不加密<br>
推荐使用"auto"加密方式，这样可以永久保证安全性和兼容性。<br>

## 8.动态端口-Dynamic port (:3[▓▓▓▓▓▓▓▓▓]
V2Ray 提供了一个叫动态端口的功能。顾名思义，就是可以动态变化通信端口，该功能的初衷是为了应对电信服务运营商可能会对长时间大流量的单个端口进行限速。<br>
也许是用的人比较少，到目前为止没有证据可以动态端口对于科学上网是加分项还是减分项。<br>
个人认为是加分项，而且这个脚本并没有进行一键配置，就拿出来说一下了，本动态端口是基于kcp的<br>
~~基于tcp的包含在了后面的tls里，无了，H2不支持动态端口，tcp是自己支持的，想实现的小伙伴可以仿照kcp的动态端口实现~~<br>
### 注意这里修改值的话服务器端与客户端都要在config.json文件内修改并且在V2RayN的配置中进行相应修改并重启v2ray服务
境外VPS服务器端：<br>
```Json
{
  "inbounds": [
    {
      "port": 自己设定的端口号,
      "protocol": "vmess",
      "settings": {
        "clients": [
          {
            "id": "自主生成合法id",
            "level": 1,
            "alterId": 4
          }
        ],
        "detour": {        
          "to": "dynamicPort"   
        }
      },
      "streamSettings": {
        "network": "kcp"
        "security": "none",
        "tcpSettings": {},
        "httpSettings": {},
        "kcpSettings": {
          "mtu": 1350,
          "tti": 50,
          "uplinkCapacity": 100,
          "downlinkCapacity": 100,
          "congestion": false,
          "readBufferSize": 2,
          "writeBufferSize": 2,
          "header": {
            "type": "wechat-video"
          }
        },
        "wsSettings": {},
        "quicSettings": {}
      }
    },
    {
      "protocol": "vmess",
      "port": "10000-40000", // 端口范围
      "tag": "dynamicPort",       
      "settings": {
        "default": {
          "level": 1,
          "alterId": 4
        }
      },
      "allocate": {            // 分配模式
        "strategy": "random",  // 随机开启
        "concurrency": 2,      // 同时开放两个端口
        "refresh": 3           // 每三分钟刷新一次
      }
    }
  ]
}
```
客户端：<br>
```Json
{
  "outbounds": [
    {
      "protocol": "vmess",
      "settings": {
        "vnext": [
          {
            "address": "境外vpsIP",
            "port": 自己设定的端口号,
            "users": [
              {
                "id": "自主生成合法id",
                "alterId": 4,
                "email": "t@t.tt",
                "security": "auto"
              }
            ]
          }
        ]
      },
      "streamSettings": {
        "network": "kcp"
      }
    }
  ]
}
```

## 9.Tls-Transport layer security (●´∀｀●)
TLS中文译名是传输层安全，HTTPS就是基于TLS上的协议，但是个人其实并不建议KCP + TLS的方案，选用KCP的原因是为了在某些恶劣的网络环境下拥有比较好的上网体验。<br>
而使用 TLS 的原因大约有两种考虑：一是认为 TLS 拥有与 HTTPS 一样的特征不容易被墙；二是觉得TLS具有更好的加密效果不容易被墙。<br>
对于第一点，尽管 HTTPS 是基于 TLS，但并不等同与 TLS，因此 TLS 与 HTTPS 的特征一样的说法是错误的；<br>
对于第二点，使用更强的加密算法而被墙的几率更小这个观点并未得到论证。<br>
然而这并不是我不推荐的理由，真正的原因的是TLS是在更上一层的协议，他必须建立在可靠的数据传输基础上，所以一般是在TCP之上。<br>
TLS最合适的搭配应该是WebSocket + TLS + Web或者TLS + HTTP/2<br>
### 9.1 注册一个域名
建议godaddy家的整一个便宜一点的，虽然理论上github自带的免费域名仓库也可以用，但是我没尝试过，并且有个个人域名是个很方便的事情<br>

### 9.2 添加域名指向
注册后，在自己的域名管理界面找到DNS相关的管理，添加A类指向自己的境外VPSIP<br>

记得要同时删除掉原有的A类指向parked<br>

### 9.3 生成证书
境外VPS服务器端安装acme.sh脚本：<br>
```Linux
curl  https://get.acme.sh | sh
```
安装成功后执行 `source ~/.bashrc` 以确保脚本所设置的命令别名生效。<br>
执行以下命令使用acme.sh生成证书：<br>
```Linux
~/.acme.sh/acme.sh --issue -d 个人域名(不要www.) --standalone -k ec-256
```

### 9.4 证书更新
由于 Let's Encrypt 的证书有效期只有 3 个月，因此需要 90 天至少要更新一次证书，acme.sh 脚本会每 60 天自动更新证书。也可以手动更新。<br>
手动更新 ECC 证书：<br>
```linux
~/.acme.sh/acme.sh --renew -d 个人域名(不要www.) --force --ecc
```
由于本例中将证书生成到 `/etc/v2ray/` 文件夹，更新证书之后还得把新证书生成到 `/etc/v2ray`。

### 9.5 安装证书和密钥
将ecc证书和密钥安装到 `/etc/v2ray `中：<br>
```Linux
~/.acme.sh/acme.sh --installcert -d 个人域名(不要www.) --fullchainpath /etc/v2ray/v2ray.crt --keypath /etc/v2ray/v2ray.key --ecc
```

### 9.6 配置V2Ray
### 注意这里修改值的话服务器端与客户端都要在config.json文件内修改并且在V2RayN的配置中进行相应修改并重启v2ray服务
境外VPS服务器端，回到`/etc/v2ray`，`vi config.json`:
```Json
{
  "inbounds": [
    {
      "port": 自己设定的端口号,
      "protocol": "vmess",    
      "settings": {
        "clients": [
          {
            "id": "自主生成合法id",
            "level": 1,
            "alterId": 4
          }
        ]
      },
      "streamSettings": {
        "network": "tcp",
        "security": "tls", // security 要设置为 tls 才会启用 TLS
        "tlsSettings": {
          "certificates": [
            {
              "certificateFile": "/etc/v2ray/v2ray.crt", // 证书文件
              "keyFile": "/etc/v2ray/v2ray.key" // 密钥文件
            }
          ]
        }
      }
    }
  ],
  "outbounds": [
    {
      "protocol": "freedom",
      "settings": {}
    }
  ]
}
```
客户端，回到`v2ray-windows-64`，notpad++打开config.json:<br>
```Json
{
  "inbounds": [
    {
      "port": 10808,
      "protocol": "socks",
      "sniffing": {
        "enabled": true,
        "destOverride": ["http", "tls"]
      },
      "settings": {
        "auth": "noauth"
      }
    }
  ],
  "outbounds": [
    {
      "protocol": "vmess",
      "settings": {
        "vnext": [
          {
            "address": "个人域名(不要www.)", // tls 需要域名，所以这里应该填自己的域名
            "port": 自己设定的端口号,
            "users": [
              {
                "id": "自主生成合法id",
                "alterId": 4
              }
            ]
          }
        ]
      },
      "streamSettings": {
        "network": "tcp",
        "security": "tls" // 客户端的 security 也要设置为 tls
      }
    }
  ]
}
```
### 9.7 安全性验证-Security verification 
其实重启服务器端V2Ray服务后，利用V2RayN能够访问网页就是配置成功了<br>

关于安全性验证可以将`port：自己设定的端口号`在服务器端和客户端设定为443后，利用Qualys SSL Labs's SSL Server Test，在网页中输入自己的域名（可能要带www）进行测试<br>

## 10.HTTP/2 + TLS + ~~动态端口~~ ♪（＾∀＾●）
HTTP/2相对HTTP/1.1来说网页的载入速度会有较大提升，但现在有没有较大面积普及呢？<br>
由 HTTP/2 的建议，客户端和服务器必须同时开启 TLS 才可以正常使用这个传输方式。<br>
这个方案是我目前在使用的方案，不知道之后会不会换成WebSockert + tls + web（咕咕咕）<br>
### 注意这里修改值的话服务器端与客户端都要在config.json文件内修改并且在V2RayN的配置中进行相应修改并重启v2ray服务
境外VPS服务端：<br>
```Linux
{
  "inbounds": [
    {
      "port": 443,
      "protocol": "vmess",
      "settings": {
        "clients": [
          {
            "id": "自主生成合法id",
            "alterId": 64
          }
        ]
        
      },
      "streamSettings": {
        "network": "h2", // h2 也可写成 http，效果一样
        "httpSettings": { //此项是关于 HTTP/2 的设置
          "path": "/ray"
        },
        "security": "tls", // 配置tls
        "tlsSettings": {
          "certificates": [
            {
              "certificateFile": "/etc/v2ray/v2ray.crt", // 证书文件，详见 tls 小节
              "keyFile": "/etc/v2ray/v2ray.key" // 密钥文件
            }
          ]
        }
      }
    }
    
  ],
  "outbounds": [
    {
      "protocol": "freedom",
      "settings": {}
    }
  ]
}
```
客户端：<br>
```Linux
{
  "inbounds": [
    {
      "port": 10808,
      "listen": "127.0.0.1",
      "protocol": "socks",
      "sniffing": {
        "enabled": true,
        "destOverride": ["http", "tls"]
      },
      "settings": {
        "auth": "noauth",
        "udp": true
      }
    }
  ],
  "outbounds": [
    {
      "protocol": "vmess",
      "settings": {
        "vnext": [
          {
            "address": "个人域名(不要www.)",
            "port": 443,
            "users": [
              {
                "id": "自主生成合法id",
                "alterId": 4
              }
            ]
          }
        ]
      },
      "streamSettings": {
        "network": "h2",
        "httpSettings": { //此项是关于 HTTP/2 的设置
          "path": "/ray"
        },
        "security": "tls"
      }
    }
  ]
}
```

## 11.好的，又写到这里了，嗯……还是没有配图，毕竟git提交一次性解决更好，不然觉得会很麻烦（其实就是懒x），过些时候再写W + T + W吧，还有各种代理穿透之类的QWQ
