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

[透明代理配置教程](https://docs.mitmproxy.org/stable/howto-transparent/)


```
# 开启系统级流量重定向
iptables -t nat -A OUTPUT -o lo -j RETURN
iptables -t nat -A PREROUTING -i lo -j RETURN
iptables -t nat -A OUTPUT -d 192.168.1.100 -j RETURN
iptables -t nat -A PREROUTING -p tcp --dport 80 -j REDIRECT --to-port 8080
iptables -t nat -A PREROUTING -p tcp --dport 443 -j REDIRECT --to-port 8080
iptables -t nat -A OUTPUT -p tcp --dport 80 -j REDIRECT --to-port 8080
iptables -t nat -A OUTPUT -p tcp --dport 443 -j REDIRECT --to-port 8080

# 清除规则
iptables -t nat -F
```

---


## win系统安装 mitmproxy

### 1. 下载 Windows 安装包
1. 访问 [mitmproxy 官网下载](https://mitmproxy.org/)。
2. 下载并安装适合 Windows 的 `.exe` 安装包。

## 2. 启动 mitmproxy
1. 打开 **命令提示符** 或 **PowerShell**。
2. 启动 mitmproxy，默认监听 `8080` 端口：
    ```
    mitmproxy --listen-host 0.0.0.0 --listen-port 8080 --web-host 0.0.0.0 --web-port 8081
    ```

## 3. 配置系统使用代理
### 设置系统代理
1. 打开 **设置** -> **网络和互联网** -> **代理**。
2. 在 **手动代理设置** 中，开启 **使用代理服务器**。
   - 代理地址：`127.0.0.1`
   - 端口：`8080`

### 命令行设置系统代理
1. 通过PowerShell设置全局代理：

`netsh winhttp set proxy 127.0.0.1:8080`

2. 验证代理设置：

`netsh winhttp show proxy`

## 4. 信任 mitmproxy 证书

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

### 在浏览器中安装证书
1. **Chrome/Edge**：
    - 打开 **设置** -> **隐私与安全** -> **安全** -> **管理证书**。
    - 导入证书并信任它。
2. **Firefox**：
    - 打开 **设置** -> **隐私与安全** -> **证书** -> **查看证书**。
    - 导入并信任证书。

## 5. 测试代理
1. 打开浏览器访问任意网站，查看 `mitmproxy` 控制台是否显示日志。

## 常见问题
- **证书未信任**：确保证书已正确安装并信任。可以尝试重新启动系统以确保证书生效。
- **代理不生效**：确认代理配置正确，`mitmproxy` 正在运行。
- **某些应用未生效**：某些应用程序可能不遵循系统代理设置，需单独配置代理。
