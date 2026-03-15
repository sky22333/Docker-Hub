**创建白名单镜像：**

```
docker run -d --name mtproxy \
  --restart=always \
  -e domain="swift.com" \
  -p 8080:80 \
  -p 8443:443 \
  ellermister/mtproxy
```

  

**创建普通镜像：**

```
docker run -d --name mtproxy \
  --restart=always \
  -e domain="swift.com" \
  -e secret="548593a9c0688f1f7d9d57377897d321" \
  -e ip_white_list="OFF" \
  -p 8080:80 \
  -p 8443:443 \
  ellermister/mtproxy
```

`-e ip_white_list="OFF"`   此行表示关闭白名单

`-e secret="548593a9c0688f4f7d9d57377897d964"`   此行表示指定密钥，格式为：32位十六进制字符。

`-e provider=2 \`      此行代表MTG第三方代理程序， 官方只支持 x86_64

**在日志中查看链接参数配置**：

```
docker logs -f mtproxy
```


连接端口记得修改为你映射后的外部端口，如上文例子中都是 8443

白名单镜像默认所有访客都不被允许连接，只有当访客尝试访问了下面的地址，才会将访客IP加入到白名单中。
IP 和端口取决于你 docker 的配置：
```
http://服务器IP/add.php
```

## rust版本TG代理
项目地址：https://github.com/telemt/telemt
#### 生成密钥：
```
openssl rand -hex 16
```
#### `docker-compose.yml`配置
```
services:
  telemt:
    image: ghcr.io/telemt/telemt:latest
    restart: unless-stopped
    ports:
      - "443:443"
      - "127.0.0.2:9091:9091"
    volumes:
      - ./config.toml:/run/telemt/config.toml:ro
    working_dir: /run/telemt
    read_only: true
    security_opt:
      - no-new-privileges:true
    cap_drop:
      - ALL
    cap_add:
      - NET_BIND_SERVICE
    ulimits:
      nofile:
        soft: 65536
        hard: 65536
```
#### `config.toml`配置
```
# === 频道推广 ===
[general]
use_middle_proxy = true
# ad_tag = "00000000000000000000000000000000"
# @MTProxybot：将用户密钥发送给此机器人，然后输入机器人返回的tag密钥，记得取消注释，TG账户需要2019年以前的

# === 日志级别 ===
# 日志级别: debug | verbose | normal | silent
log_level = "silent"

[general.modes]
classic = false
secure = false
tls = true

[general.links]
show = "*"

# === 服务器绑定 ===
[server]
port = 443
# proxy_protocol = false           # 如果反代并启用了 PROXY protocol，需要开启
# metrics_port = 9090
# metrics_whitelist = ["127.0.0.1", "::1", "0.0.0.0/0"]

[server.api]
enabled = true
listen = "0.0.0.0:9091"
whitelist = ["127.0.0.0/8", "172.16.0.0/12"]
minimal_runtime_enabled = false
minimal_runtime_cache_ttl_ms = 1000

# 在多个接口/IP上监听 - IPv4
[[server.listeners]]
ip = "0.0.0.0"

# === 反审查与伪装 ===
[censorship]
tls_domain = "www.bing.com"
mask = true
tls_emulation = true        # 获取真实证书长度并模拟 TLS 记录
tls_front_dir = "tlsfront"   # TLS 模拟缓存目录

[access.users]
# 格式: "username" = "32位十六进制secret"
user1 = "00000000000000000000000000000000"
```

#### 获取链接
```
curl -s http://127.0.0.2:9091/v1/users | jq
```

---
