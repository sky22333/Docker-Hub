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
#frp监听的端口，默认是7000，可以改成其他的
bind_port = 7000
#授权码，请改成更复杂的
token = 1314520asd

#frp管理后台端口，请按自己需求更改
dashboard_port = 7500
#frp管理后台用户名和密码，请改成自己的
dashboard_user = admin
dashboard_pwd = admin
enable_prometheus = true

#frp日志配置
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
```
