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
token = 52010  # 这个token之后在客户端会用到
#http监听端口
vhost_http_port = 6001
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
server_addr = x.x.x.x      # 服务端公网IP
server_port = 7000         # 与服务端bind_port一致
token = 52010              # 与服务端的token一致

[ssh]
type = tcp
local_ip = 127.0.0.1
local_port = 22
remote_port = 6000         # 这个自定义，之后再ssh连接的时候要用

[web]
type = http
local_ip = 127.0.0.1
local_port = 8080
custom_domains = 自定义域名my.test.com
[web2]
type = http
local_ip = 127.0.0.1
local_port = 80
custom_domains = 自定义域名my.test.com
```
