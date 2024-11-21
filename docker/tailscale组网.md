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
