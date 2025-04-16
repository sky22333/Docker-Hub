### `astrbot`QQ微信机器人支持大模型

`docker-compose.yml`
```
services:
  astrbot:
    image: soulter/astrbot:latest
    container_name: astrbot
    ports:
      - "6180-6200:6180-6200"
      - "11451:11451"
    volumes:
      - ./data:/AstrBot/data
    tty: true
    stdin_open: true
    restart: always

  caddy:
    image: caddy:alpine
    container_name: caddy
    restart: always
    cap_add:
      - NET_ADMIN
    ports:
      - "80:80"
      - "443:443"
      - "443:443/udp"
    volumes:
      - ./Caddyfile:/etc/caddy/Caddyfile
```

`Caddyfile`
```
bot.example.com {
    reverse_proxy astrbot:6196
}
```

面板后台端口`11451`

[官方地址](https://github.com/Soulter/AstrBot)
