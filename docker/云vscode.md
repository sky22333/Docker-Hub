### Docker部署云vscode编辑器

创建项目文件
```
mkdir -p code-server && cd code-server && touch docker-compose.yaml
```



`docker-compose.yaml`配置

```
services:
  code-server:
    image: linuxserver/code-server:latest
    container_name: code-server
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=Asia/Shanghai
      - PASSWORD=admin123      # 设置code-server的登录密码
      - SUDO_PASSWORD=         # 设置服务器sudo权限的密码（可选）
    volumes:
      - /:/config/workspace
    ports:
      - 8443:8443
    privileged: true
    cap_add:
      - ALL
    security_opt:
      - seccomp:unconfined
      - apparmor:unconfined
    restart: always
```


