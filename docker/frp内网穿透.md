### Docker部署frp内网穿透

>快速启动
>
>`docker run --restart=always --network host -d -v ./frps.toml:/etc/frp/frps.toml --name frps snowdreamtech/frps`



#### 服务端（有公网IP）
```
touch docker-compose.yaml frps.toml
```

```
services:
    frps:
        restart: always
        network_mode: host
        volumes:
            - './frps.toml:/etc/frp/frps.toml'
        container_name: frps
        image: snowdreamtech/frps
```
`frps.toml`配置
```
# 连接参数和客户端保持一致
[common]
bind_port = 7000
token = 1314520asd

# frp后台管理信息（可以去掉）
dashboard_port = 7500
dashboard_user = admin123
dashboard_pwd = admin321
enable_prometheus = true

# frp日志配置
log_file = /var/log/frps.log
log_level = info
log_max_days = 3
```

---

#### 客户端（内网）
```
touch docker-compose.yaml frpc.toml
```

```
services:
    frpc:
        restart: always
        network_mode: host
        volumes:
            - './frpc.toml:/etc/frp/frpc.toml'
        container_name: frpc
        image: snowdreamtech/frpc
```

`frpc.toml`配置
```
# 连接服务端参数
[common]
server_addr = 服务端公网IP
server_port = 7000
token = 1314520asd

# 本地服务
[web]
type = tcp
local_ip = 127.0.0.1
local_port = 54321
remote_port = 9999

# 本地服务
[web2]
type = tcp
local_ip = 127.0.0.1
local_port = 8080
remote_port = 9998

# SSH隧道
[ssh]
name = "ssh"
type = "tcp"
localIP = "127.0.0.1"
localPort = 22
remotePort = 2222

# 本地文件对外访问
[wenjian]
name = "static"
type = "tcp"
# 服务端公网端口
remotePort = 6000
[proxies.plugin]
type = "static_file"
# 本地文件目录
localPath = "/tmp/file"
# 服务端访问文件的路径
stripPrefix = "static"
httpUser = "admin"
httpPassword = "Password"
```
- 公网web地址示例：`http://x.x.x.x:9999`
- 公网文件地址示例：`http://x.x.x.x:6000/static`
- SSH访问示例：`ssh -o Port=2222 root@x.x.x.x`

---

#### win系统的docker客户端配置

- 配置文件中`local_ip`的值改为`host.docker.internal`则可以访问宿主机的服务
- 修改`docker-compose`配置映射公网端口供服务端访问
```
services:
  frpc:
    restart: always
    container_name: frpc
    image: snowdreamtech/frpc
    volumes:
      - './frpc.toml:/etc/frp/frpc.toml'
    ports:
      - "9999:9999"
      - "2222:2222"
      - "6000:6000"
```

---

[官方安装包](https://github.com/fatedier/frp/releases)

服务端启动命令`./frps -c ./frps.toml`

客户端启动命令`./frpc -c ./frpc.toml`
