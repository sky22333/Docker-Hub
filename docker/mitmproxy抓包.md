## `mitmproxy`抓包工具Linux

下载地址：https://www.mitmproxy.org/downloads/

### Docker部署
```
docker run -d \
  --name mit \
  --network host \
  -v "$(pwd)/mitmproxy:/home/mitmproxy/.mitmproxy" \
  --restart always \
  mitmproxy/mitmproxy \
  mitmweb --listen-host 0.0.0.0 --listen-port 8080 --web-host 0.0.0.0 --web-port 8081
```

默认`8080`(代理端口)，和`8081`(面板端口)

证书在挂载的目录，`Debian`系统信任证书：
```
sudo cp ./mitmproxy/mitmproxy-ca-cert.pem /usr/local/share/ca-certificates/mitmproxy.crt
```
```
sudo update-ca-certificates
```

打开面板查看流量日志

linux可配合 [daed](https://github.com/daeuniverse/daed) 透明代理捕获系统所有流量

## win系统安装 mitmproxy

### 下载 Windows 安装包

访问 [mitmproxy 官网下载](https://mitmproxy.org/)。

### 启动 mitmproxy
```
mitmweb --listen-host 0.0.0.0 --listen-port 8080 --web-host 0.0.0.0 --web-port 8081
```

### 设置系统代理
```
127.0.0.1:8080
```

可配合 [proxifyre-ui](https://github.com/sky22333/proxifyre-ui) 透明代理捕获系统所有流量

### 导入系统根证书
管理员 PowerShell：
```
certutil -addstore Root "$HOME\.mitmproxy\mitmproxy-ca-cert.cer"
```
