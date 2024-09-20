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
      - FRPS_VHOST_HTTP_PORT=80       # HTTP 类型反向代理监听的端口
      - FRPS_DASHBOARD_PORT=7500      # Dashboard 监听的端口
      - FRPS_DASHBOARD_USER=admin     # Dashboard 登录用户名
      - FRPS_DASHBOARD_PWD=admin      # Dashboard 登录密码
      - FRPS_TOKEN=your_token_here    # 用于客户端连接的身份验证令牌
    ports:
      - "7000:7000"
      - "80:80"
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
      
      # 示例：SSH 服务转发
      - FRPC_SSH_TYPE=tcp
      - FRPC_SSH_LOCAL_IP=host.docker.internal  # 使用这个特殊 DNS 名称访问宿主机
      - FRPC_SSH_LOCAL_PORT=22
      - FRPC_SSH_REMOTE_PORT=6000     # 在 frps 服务器上暴露的远程端口
    extra_hosts:
      - "host.docker.internal:host-gateway"  # 允许容器访问宿主机
```
