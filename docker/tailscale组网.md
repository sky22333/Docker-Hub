### Docker部署`tailscale`内网组网


#### 使用链接登录
```
docker run -d \
  --name=tailscale \
  --restart=always \
  --network=host \
  --privileged \
  -v $(pwd)/tailscale:/var/lib/tailscale \
  tailscale/tailscale \
  tailscaled --no-auth-key
```

生成认证链接，在浏览器中登录：
```
docker exec -it tailscale tailscale up
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
    command: ["tailscaled", "--auth-key=<你的AuthKey>"]
```



---

[官网](https://tailscale.com/)
