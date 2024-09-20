### docker部署frp内网穿透

#### 服务端（有公网IP）
```
services:
    frps:
        restart: always
        network_mode: host
        volumes:
            - './frps.ini:/etc/frp/frps.ini'
        container_name: frps
        image: snowdreamtech/frps
```

服务端`frps.ini`配置
```
[common]
#frp监听端口，与客户端绑定端口
bind_port= 5443
kcp_bind_port = 5443

#面板用户名
dashboard_user= admin123

#面板密码
dashboard_pwd= admin7890

#面板端口
dashboard_port= 9527

#设置token服务端和客户端要一致
token = 8ad3d1x42aaa78890
```

#### 客户端（内网）
```
services:
    frpc:
        restart: always
        network_mode: host
        volumes:
            - './frpc.ini:/etc/frp/frpc.ini'
        container_name: frpc
        image: snowdreamtech/frpc
```

客户端`frpc.ini`配置
```
[common]
server_addr = 8.8.8.8          # 服务端公网地址
server_port = 5443             # 服务端端口
token = 8ad3d1x42aaa78890      # token和服务端一致

[ssh]
type = tcp
local_ip = 127.0.0.1
local_port = 22             # 本地端口
remote_port = 6000          # 服务端公网端口

[web]
type = tcp
local_ip = 127.0.0.1
local_port = 80            # 本地端口
remote_port = 8080         # 服务端公网端口
```
