# Hysteria协议安装

- Project Heysteria：https://github.com/apernet/hysteria
- Hysteria Offical Blog: https://v2.hysteria.network

## 服务端部署
- 更新 VPS 系统安装所需组件

Debian命令
```
apt update -y
```
```
apt install curl sudo -y
```
CenOS命令
```
yum update -y
```
```             
yum install curl sudo -y
```

- Hysteria 官方脚本

安装或升级到最新版本 Hysteria 2
```
bash <(curl -fsSL https://get.hy2.sh/)
```
移除 Hysteria 2
```
bash <(curl -fsSL https://get.hy2.sh/) --remove
```

## SSL证书申请
- bing 一键自签脚本
```
openssl req -x509 -nodes -newkey ec:<(openssl ecparam -name prime256v1) -keyout /etc/hysteria/server.key -out /etc/hysteria/server.crt -subj "/CN=bing.com" -days 36500 && sudo chown hysteria /etc/hysteria/server.key && sudo chown hysteria /etc/hysteria/server.crt
```
- acme.sh 手动安装
```
wget -N --no-check-certificate https://raw.githubusercontent.com/Felix-zf/ACME-Scripts/main/acme.sh && bash acme.sh
```
## Hysteria2 相关命令

启动hy2
```
systemctl start hysteria-server.service
```
开机自启
```
systemctl enable hysteria-server.service
```
重启Hysteria2
```
systemctl restart hysteria-server.service
```
查看Hysteria2状态
```
systemctl status hysteria-server.service
```

## 服务端配置
*服务端的配置要和客户端状态一致，也就是说，服务端配置的是无域名的配置，客户端也必须选择无域名的配置参数，必须一致，反之一样，服务端配置的是有域名的，那稍后客户端也必须选择有域名的参数*

- 编辑配置文件
```
vi /etc/hysteria/config.yaml
```
- 配置文件内容
```
listen: :443
 
# 以下 acme 和 tls 字段，二选一
# 有域名部署的选择 acme ，无域名的选择 tls
# 选择 acme，必须注释掉 tls，反之一样
 
#acme:
#  domains:
#    - cn2.bozai.us        # 域名
#  email: yourself@email.com   # 邮箱，格式正确即可
 
tls:
  cert: /etc/hysteria/server.crt
  key: /etc/hysteria/server.key
 
auth:
  type: password
  password: 88888888   # 请及时更改密码
 
masquerade:
  type: proxy
  proxy:
    url: https://addons.mozilla.org # 伪装网站
    rewriteHost: true
```

## 客户端配置 
### Windows 推荐使用 V2rayN / Hiddfy Next
*请看清楚配置文件中的注释，修改 ip , auth（VPS 服务端 上面配置的密码） ， bandwidth ， sni ， insecure 等参数*
```
server: ip:443
auth: ****
 
#bandwidth:
#  up: 30 mbps
#  down: 90 mbps
  
tls:
  sni: www.bing.com  # 若无域名，请改为 bing.com
  insecure: true    # 若无域名，需要改参数为 true
 
socks5:
  listen: 127.0.0.1:1080
http:
  listen: 127.0.0.1:8080
```

*Tips: 若是网络拥挤，丢包率高，我们在填入了 UP 和 DOWN 的数值带宽数值以后，Hysteria 会通过计算丢包率来提升速度进行补偿*

方法一
- 首先在此：https://github.com/apernet/hysteria/releases/ 下载客户端,解压至v2rayN的bin/hysteria目录中.
- 打开 v2rayN，依次点击“服务器”→“添加自定义服务器”,输入别名、导入脚本生成的文件，Core类型选择hysteria，Socks端口输入xxxx.

方法二  
- 新建heysteria2服务器，填入地址端口密码，开启跳过证书验证

### Android / IOS / MacOS 配置
sing-box 配置文件
```
{
  "dns": {
    "servers": [
      {
        "tag": "cf",
        "address": "https://1.1.1.1/dns-query"
      },
      {
        "tag": "local",
        "address": "223.5.5.5",
        "detour": "direct"
      },
      {
        "tag": "block",
        "address": "rcode://success"
      }
    ],
    "rules": [
      {
        "geosite": "category-ads-all",
        "server": "block",
        "disable_cache": true
      },
      {
        "outbound": "any",
        "server": "local"
      },
      {
        "geosite": "cn",
        "server": "local"
      }
    ],
    "strategy": "ipv4_only"
  },
  "inbounds": [
    {
      "type": "tun",
      "inet4_address": "198.18.0.1/16",
      "auto_route": true,
      "strict_route": false,
      "sniff": true
    }
  ],
  "outbounds": [
    {
      "type": "hysteria2",
      "tag": "proxy",
      "server": "ip",             //服务器 IP地址
      "server_port": 443,
      "up_mbps": 30,              //上传速率，实际填写，过大会导致流量浪费
      "down_mbps": 90,           //下载速率，实际填写，过大会导致流量浪费
      "password": "**********",   //hysteria2 服务密码
      "tls": {
        "enabled": true,
        "server_name": "www.bing.com",    //若域名搭建，请填写域名，若IP搭建，请填写 bing.com
        "insecure": true                 //若域名搭建，请填写 false，若IP搭建，请填写 true
      }
    },
    {
      "type": "direct",
      "tag": "direct"
    },
    {
      "type": "block",
      "tag": "block"
    },
    {
      "type": "dns",
      "tag": "dns-out"
    }
  ],
  "route": {
    "rules": [
      {
        "protocol": "dns",
        "outbound": "dns-out"
      },
      {
        "geosite": "cn",
        "geoip": [
          "private",
          "cn"
        ],
        "outbound": "direct"
      },
      {
        "geosite": "category-ads-all",
        "outbound": "block"
      }
    ],
    "auto_detect_interface": true
  }
}
```
*请看清楚以上配置文件中的注释，根据自己的需要，自行更改。*

## Hysteria2相关命令
#启动Hysteria2
```
systemctl start hysteria-server.service
```
#停止Hysteria2
```
systemctl stop hysteria-server.service
```
#设置开机自启
```
systemctl enable hysteria-server.service
```
#重启Hysteria2
```
systemctl restart hysteria-server.service
```
#查看Hysteria2状态(按q退出)
```
systemctl status hysteria-server.service
```
#查看日志
```
journalctl -u hysteria-server.service
```
*Tips: 部分厂商VPS如搭建完Hysteria2无法正常启动，请执行如下命令添加私钥的读写权限，完成之后执行重启Hysteria2命令，再执行查看Hysteria2状态命令，如果启动成功即可！*
```
chmod +rw /root/private.key
```
#Hysteria 2 的伪装验证  

Windows 命令提示符进入 Chrome.exe 目标地址（cd+路径），开启 Hysteria2 代理，客户端打开 TUN 模式，cmd 命令如下：
```
chrome --origin-to-force-quic-on=你的域名:443
```
访问 https://你的域名 ,若是按照上述搭建的话，会跳转到 bing.com 的网页

## 端口跳跃
- 在基于Ubuntu和基于Debian的Linux发行版上，运行以下命令来安装ifconfig：
```
sudo apt install net-tools -y
```
- 用`ifconfig`命令查看网卡名称，默认一般是eth0。然后在终端里输入这条命令：
```
# IPv4
iptables -t nat -A PREROUTING -i eth0 -p udp --dport 20000:50000 -j REDIRECT --to-ports 443
# IPv6
ip6tables -t nat -A PREROUTING -i eth0 -p udp --dport 20000:50000 -j REDIRECT --to-ports 443
```
在这个示例中，服务器监听 443 端口，但客户端可以通过 20000-50000 范围内的任何端口连接。

## Debian系统命令放行端口
- 安装iptables（通常系统都会自带，如果没有就需要安装）
```
apt-get update
```
```
apt-get install iptables
```
- 放行端口
```
iptables -I INPUT -p tcp --dport xxxx -j ACCEPT
```
- 保存放行规则
```
iptables-save
```
- 设置完就已经放行了指定的端口，但重启后会失效，下面设置持续生效规则；
- 安装iptables-persistent
```
apt-get install iptables-persistent
```
- 保存规则持续生效
```
netfilter-persistent save
```
```
netfilter-persistent reload
```
设置完成后指定端口就会持续放行了

## 鸣谢
- 油管up主波仔分享的blog教学：https://v2rayssr.com/hysteria2.html
- Misaka-blog的heysteria项目：https://github.com/Misaka-blog/hysteria-install  



