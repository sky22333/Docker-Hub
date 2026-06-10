## 使用docker-compose部署`mitmproxy`抓包工具
```
services:
  mitmproxy:
    image: mitmproxy/mitmproxy
    container_name: mit
    network_mode: "host"
    volumes:
      - ./mitmproxy:/home/mitmproxy/.mitmproxy
    command: mitmweb --web-host 0.0.0.0 --web-port 8081
```

默认占用`8080`(代理端口)，和`8081`(面板端口)

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

### 1. 下载 Windows 安装包
1. 访问 [mitmproxy 官网下载](https://mitmproxy.org/)。
2. 下载并安装适合 Windows 的 `.exe` 安装包。

## 2. 启动 mitmproxy
1. 打开 **命令提示符** 或 **PowerShell**。
2. 启动 mitmproxy，默认监听 `8080` 端口：
    ```
    mitmweb --listen-host 0.0.0.0 --listen-port 8080 --web-host 0.0.0.0 --web-port 8081
    ```

### 设置系统代理
1. 打开 **设置** -> **网络和互联网** -> **代理**。
2. 在 **手动代理设置** 中，开启 **使用代理服务器**。
   - 代理地址：`127.0.0.1`
   - 端口：`8080`

可配合 [proxifyre-ui](https://github.com/sky22333/proxifyre-ui) 透明代理捕获系统所有流量

### 下载 mitmproxy 证书
1. 在浏览器中访问 `http://mitm.it`。
2. 下载并安装根证书。

### 安装证书到 Windows 系统（全局信任）

1. **打开证书管理器**：
   - 按下 **Win + R** 键，输入 `certmgr.msc` 并按回车，打开证书管理器。
   
2. **导入 mitmproxy 证书**：
   - 在证书管理器中，右键点击 **受信任的根证书颁发机构** 文件夹，选择 **所有任务** -> **导入**。
   - 在导入向导中选择下载的证书文件（`mitmproxy-ca-cert.pem`），然后选择 **放入以下存储**，并选择 **受信任的根证书颁发机构**。
   - 完成导入并确认证书已经被信任。
