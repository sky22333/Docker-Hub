### Docker部署frp内网穿透

**可选项：`Docker CLI`启动命令示例**

```
docker run --restart=always --network host -d -v ./frps.toml:/etc/frp/frps.toml --name frps snowdreamtech/frps
```

### `docker-compose`部署服务端（有公网IP）

创建配置文件
```
touch docker-compose.yml frps.toml
```
`docker-compose.yaml`配置
```
services:
  frps:
    image: snowdreamtech/frps
    container_name: frps
    network_mode: host
    volumes:
      - ./frps.toml:/etc/frp/frps.toml
    restart: always
```
服务端`frps.toml`配置
```
bindPort = 7000
auth.token = "admin123"
```

---

### `docker-compose`部署客户端（内网）

创建配置文件
```
touch docker-compose.yml frpc.toml
```
`docker-compose.yaml`配置
```
services:
  frpc:
    image: snowdreamtech/frpc
    container_name: frpc
    network_mode: host
    volumes:
      - ./frpc.toml:/etc/frp/frpc.toml
    restart: always
```

客户端`frpc.toml`配置
```
serverAddr = "8.8.8.8"      # 公网服务器IP
serverPort = 7000           # 对接端口和frps一致
auth.token = "admin123"     # 认证令牌和frps一致

# 暴露SSH服务
[[proxies]]
name = "ssh"
type = "tcp"
localIP = "127.0.0.1"
localPort = 22            # 本地服务端口
remotePort = 6000         # 让frps暴露的公网端口
```

---


### 二进制文件部署

官方二进制文件下载地址：https://github.com/fatedier/frp/releases

配置文件和上面一样

服务端启动命令`./frps -c ./frps.toml`

客户端启动命令`./frpc -c ./frpc.toml`
