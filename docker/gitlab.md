### Docker部署自托管gitlab

```
services:
  gitlab:
    image: gitlab/gitlab-ce:latest
    container_name: gitlab
    restart: always
    volumes:
      - './gitlab-data:/var/opt/gitlab'
      - './gitlab-config:/etc/gitlab'
      - './gitlab-logs:/var/log/gitlab'
    environment:
      GITLAB_OMNIBUS_CONFIG: |
        external_url 'https://example.com'  # 设置域名

  caddy:
    image: caddy:alpine
    container_name: caddy
    ports:
      - "80:80"
      - "443:443"
    volumes:
      - ./Caddyfile:/etc/caddy/Caddyfile
    restart: always

```

`Caddyfile`配置

```
https://example.com {
    encode zstd gzip
    reverse_proxy gitlab:80

    # 启用 WebSocket 连接
    header {
        Upgrade        "websocket"
        Connection     "upgrade"
    }
}
```
