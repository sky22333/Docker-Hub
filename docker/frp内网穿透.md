### docker部署frp内网穿透

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
[common]
# frp默认监听端口
bind_port = 7000
# 授权码
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
# 客户端配置
[common]
server_addr = x.x.x.x
server_port = 7000
token = 1314520asd

# 本地服务
[web]
type = tcp
local_ip = 127.0.0.1
local_port = 54321
remote_port = 9999

[web2]
type = tcp
local_ip = 127.0.0.1
local_port = 8080
remote_port = 9998
```

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
      - "9998:9998"
```
