# Heysteria-install

- Project Heysteria：https://github.com/apernet/hysteria
- Offical Blog: https://v2.hysteria.network

## 服务端部署步骤 
- 更新 VPS 系统安装所需组件

#Debian命令
```
apt update -y
```
```
apt install curl sudo -y
```
#CenOS命令
```
yum update -y
```
```             
yum install curl sudo -y
```

- Hysteria 官方的一键安装脚本

#安装或升级到最新版本 Hysteria 2
```
bash <(curl -fsSL https://get.hy2.sh/)
```
#移除 Hysteria 2
```
bash <(curl -fsSL https://get.hy2.sh/) --remove
```

- Hysteria 一键安装脚本

#hysteria 2
```
wget -N --no-check-certificate https://raw.githubusercontent.com/Felix-zf/Heysteria-Scripts/main/hy2/hysteria.sh && bash hysteria.sh
```
#hysteria 1
```
wget -N --no-check-certificate https://raw.githubusercontent.com/Felix-zf/Heysteria-Scripts/main/hy1/hysteria.sh && bash hysteria.sh
```

- 下载服务端文件: sz+相应文件路径

## SSL证书申请
- 若有域名，acme.sh 证书一键生成
```
wget -N --no-check-certificate https://raw.githubusercontent.com/Felix-zf/ACME-Scripts/main/acme.sh && bash acme.sh
```
- 若无域名，生成自签证书
```
openssl req -x509 -nodes -newkey ec:<(openssl ecparam -name prime256v1) -keyout /etc/hysteria/server.key -out /etc/hysteria/server.crt -subj "/CN=bing.com" -days 36500 && sudo chown hysteria /etc/hysteria/server.key && sudo chown hysteria /etc/hysteria/server.crt
```

## 服务端配置文件
*服务端的配置要和客户端状态一致，也就是说，服务端配置的是无域名的配置，客户端也必须选择无域名的配置参数，必须一致，反之一样，服务端配置的是有域名的，那稍后客户端也必须选择有域名的参数*
```
listen: :443
 
# 以下 acme 和 tls 字段，二选一
# 有域名部署的选择 acme ，无域名的选择 tls
# 选择 acme，必须注释掉 tls，反之一样
 
acme:
  domains:
    - cn2.bozai.us        # 域名
  email: ityourself@email.com   # 邮箱，格式正确即可
 
#tls:
#  cert: /etc/hysteria/server.crt
#  key: /etc/hysteria/server.key
 
auth:
  type: password
  password: 88888888   # 请及时更改密码
 
masquerade:
  type: proxy
  proxy:
    url: https://bing.com # 伪装网站
    rewriteHost: true
```

## 客户端配置  
*请看清楚配置文件中的注释，修改 ip , auth（VPS 服务端 上面配置的密码） ， bandwidth ， sni ， insecure 等参数*
```
server: ip:443
auth: ****
 
#bandwidth:
#  up: 20 mbps
#  down: 100 mbps
  
tls:
  sni: cn2.bozai.us  # 若无域名，请改为 bing.com
  insecure: false    # 若无域名，需要改参数为 true
 
socks5:
  listen: 127.0.0.1:1080
http:
  listen: 127.0.0.1:8080
```

方法一
- 首先在此：https://github.com/apernet/hysteria/releases/ 下载客户端,解压至v2rayN的bin/hysteria目录中.
- 打开 v2rayN，依次点击“服务器”→“添加自定义服务器”,输入别名、导入脚本生成的文件，Core类型选择hysteria，Socks端口输入xxxx.

方法二  
- 新建heysteria2服务器，填入地址端口密码，开启跳过证书验证


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
Tips:部分厂商VPS如搭建完Hysteria2无法正常启动，请执行如下命令添加私钥的读写权限，完成之后执行重启Hysteria2命令，再执行查看Hysteria2状态命令，如果启动成功即可！
```
chmod +rw /root/private.key
```

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
- Misaka-blog的heysteria项目：https://github.com/Misaka-blog/hysteria-install  
- 油管up主波仔分享的博文教学：https://v2rayssr.com/hysteria2.html
