### docker部署frp内网穿透

#### 服务端（有公网IP）
```
touch docker-compose.yaml
```

```
services:
  frps:
    image: snowdreamtech/frps
    container_name: frps
    restart: always
    environment:
      - FRPS_BIND_PORT=7000           # frps 绑定的端口
      - FRPS_DASHBOARD_PORT=7500      # Dashboard 监听的端口
      - FRPS_DASHBOARD_USER=admin     # Dashboard 登录用户名
      - FRPS_DASHBOARD_PWD=admin      # Dashboard 登录密码
      - FRPS_TOKEN=your_token_here    # 用于客户端连接的身份验证令牌
    ports:
      - "7000:7000"
      - "7500:7500"
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
