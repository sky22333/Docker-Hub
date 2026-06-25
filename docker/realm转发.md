## Docker部署Realm流量转发程序
```
services:
  realm:
    image: ghcr.io/zhboner/realm2:latest
    container_name: realm
    restart: always
    network_mode: host
    volumes:
      - ./config.toml:/etc/realm/config.toml:ro
    command: -c /etc/realm/config.toml
```


### 传输层使用TLS加密

- 入口机配置（8899 → TLS 连落地机）
```
[[endpoints]]
listen = "0.0.0.0:8899"
remote = "B机器IP:8443"
remote_transport = "tls;sni=bing.com;insecure"
```

- 落地机配置（TLS服务端，自签证书自动生成）
```
[[endpoints]]
listen = "0.0.0.0:8443"
remote = "127.0.0.1:9900"
listen_transport = "tls;servername=bing.com"
```

### 纯端口转发
- 只需部署在入口机，同时支持tcp和udp
```
[network]
no_tcp = false
use_udp = true

[[endpoints]]
listen = "0.0.0.0:8899"
remote = "落地机:9900"
```
