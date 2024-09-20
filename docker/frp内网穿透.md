### docker部署frp内网穿透

- 服务端（有公网IP）
```
services:
  frps:
    image: snowdreamtech/frps
    container_name: frps
    environment:
      - FRP_BIND_PORT=7000         # 服务端绑定的端口
      - FRP_DASHBOARD_PORT=7500    # 仪表板端口
      - FRP_DASHBOARD_USER=admin   # 仪表板用户名
      - FRP_DASHBOARD_PWD=admin    # 仪表板密码
    ports:
      - "7000:7000"                # 连接到客户端的端口
      - "7500:7500"                # 仪表板的端口
    restart: always
```

- 客户端（内网环境）
```
services:
  frpc:
    image: snowdreamtech/frpc
    container_name: frpc
    environment:
      - FRP_SERVER_ADDR=<服务器IP>    # 服务端的IP地址
      - FRP_SERVER_PORT=7000         # 服务端监听的端口
      - FRP_LOCAL_PORT=80            # 本地需要代理的服务端口
      - FRP_REMOTE_PORT=8080         # 暴露给外网的端口
    restart: always
```
