# V2ray-install
V2ray-install test

## 服务端部署步骤  
- SSH 进入 VPS，输入以下命令
```
wget -N --no-check-certificate https://raw.githubusercontent.com/Felix-zf/V2ray-install/main/hy2/hysteria.sh && bash hysteria.sh
```

- 下载服务端文件
sz+相应文件路径

## 客户端配置
- 首先在此：https://github.com/apernet/hysteria/releases/ 下载客户端,解压至v2rayN的bin/hysteria目录中.
- 打开 v2rayN，依次点击“服务器”→“添加自定义服务器”,输入别名、导入脚本生成的文件，Core类型选择hysteria，端口输入5080.
