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
touch docker-compose.yaml
```

```
services:
  frpc:
    image: snowdreamtech/frpc
    container_name: frpc
    restart: always
    environment:
      - FRPC_SERVER_ADDR=x.x.x.x      # frps 服务器的 IP 地址
      - FRPC_SERVER_PORT=7000         # frps 服务器的端口
      - FRPC_TOKEN=your_token_here    # 与 frps 服务器相同的身份验证令牌
      - FRPC_USER=your_username       # 用户名，用于区分不同的 frpc 客户端
      
      # 内网 TCP 服务代理配置
      - FRPC_MYSERVICE_TYPE=tcp
      - FRPC_MYSERVICE_LOCAL_IP=host.docker.internal
      - FRPC_MYSERVICE_LOCAL_PORT=54321   # 本地端口
      - FRPC_MYSERVICE_REMOTE_PORT=54321  # frps 服务器上用于访问您的服务的端口
    network_mode: "bridge"
```
