# Heysteria-install
Heysteria-install test

## 服务端部署步骤 
- 更新 VPS 系统安装所需组件
```
apt update -y
```
```
apt install curl sudo -y
```

- Hysteria 2
```
wget -N --no-check-certificate https://raw.githubusercontent.com/Felix-zf/Heysteria-install/main/hy2/hysteria.sh && bash hysteria.sh
```

- Hysteria 1
```
wget -N --no-check-certificate https://raw.githubusercontent.com/Felix-zf/Heysteria-install/main/hy1/hysteria.sh && bash hysteria.sh
```

- 下载服务端文件: sz+相应文件路径

## 客户端配置  
方法一
- 首先在此：https://github.com/apernet/hysteria/releases/ 下载客户端,解压至v2rayN的bin/hysteria目录中.
- 打开 v2rayN，依次点击“服务器”→“添加自定义服务器”,输入别名、导入脚本生成的文件，Core类型选择hysteria，Socks端口输入xxxx.

方法二
新建heysteria2，填入地址端口密码，开启跳过证书验证


## Hysteria2相关命令
#启动Hysteria2
```
systemctl start hysteria-server.service
```
#查看Hysteria2状态
```
systemctl status hysteria-server.service
```
#设置开机自启
```
systemctl enable hysteria-server.service
```
#重启Hysteria2
```
systemctl restart hysteria-server.service
```
#停止Hysteria2
```
systemctl stop hysteria-server.service
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
- 例如要放行5080端口
```
iptables -I INPUT -p tcp --dport 5080 -j ACCEPT
```
- 然后保存放行规则
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

