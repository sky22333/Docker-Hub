### Docker部署`tailscale`内网组网


#### 使用链接登录
```
docker run -d \
  --name tailscale \
  --privileged \
  --network host \
  --restart always \
  -v $(pwd)/tailscale:/var/lib/tailscale \
  tailscale/tailscale:latest \
  tailscaled --state=/var/lib/tailscale/tailscaled.state
```

进入容器：
```
docker exec -it tailscale sh
```
生成登录链接：
```
tailscale up
```

或者使用密钥连接：
```
tailscale up --auth-key=<你的AuthKey>
```

查看公共节点网络
```
tailscale netcheck
```

查看是否点对点直连
```
tailscale status
```
如果看到`relay "tok"`则说明使用了公共中继连接，`tok`代表日本公共节点


#### 指定密钥运行

`docker-compose.yml`

```
services:
  tailscale:
    image: tailscale/tailscale:latest
    container_name: tailscale
    privileged: true
    network_mode: host
    restart: always
    volumes:
      - ./tailscale:/var/lib/tailscale
    command:
      - tailscaled
      - --state=/var/lib/tailscale/tailscaled.state
      - --tun=tailscale0
      - --auth-key=你的AuthKey
```
#### 宿主机部署
```
sudo apt install tailscale -y

sudo tailscale up

sudo systemctl status tailscaled
```

#### 自建`derper`

自动申请证书
```
services:
  derper:
    image: fredliang/derper:latest
    container_name: derper
    restart: unless-stopped
    environment:
      - DERP_DOMAIN=derper.your-domain.com  # 替换为您的域名
      - DERP_CERT_MODE=letsencrypt
      - DERP_STUN=true
      - DERP_HTTP_PORT=80
      - DERP_ADDR=:443
      - DERP_VERIFY_CLIENTS=true  # 本地 tailscaled 实例验证客户端，只允许自己使用
    ports:
      - "80:80"
      - "443:443"
      - "3478:3478/udp"
    volumes:
      - ./certs:/app/certs
      - /var/run/tailscale:/var/run/tailscale
    network_mode: "host"
```


| 变量名           | 必需 | 描述 | 默认值 |
|----------------|------|------|--------|
| DERP_DOMAIN   | 是   | derper 服务器主机名 | your-hostname.com |
| DERP_CERT_DIR | 否   | 存储证书的目录 (如果地址端口是 :443) | /app/certs |
| DERP_CERT_MODE| 否   | 获取证书的模式。可选项: manual, letsencrypt | letsencrypt |
| DERP_ADDR     | 否   | 服务器监听地址 | :443 |
| DERP_STUN     | 否   | 是否同时运行 STUN 服务器 | true |
| DERP_HTTP_PORT| 否   | 提供 HTTP 服务的端口。设置为 -1 禁用 | 80 |
| DERP_VERIFY_CLIENTS | 否 | 是否通过本地 tailscaled 实例验证此 DERP 服务器的客户端 | false |








---

[官网](https://tailscale.com/)

---



### CF-`Zero Trust`-`Tunnels`免费内网穿透

```
docker run -d --network host cloudflare/cloudflared:latest tunnel --no-autoupdate run --token 你的密钥
```



`docker-compose.yml`
```
services:
  cloudflared:
    image: cloudflare/cloudflared:latest
    container_name: cf
    restart: always
    command: tunnel --no-autoupdate run --token 你的密钥
```
