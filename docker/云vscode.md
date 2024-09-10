### Docker部署云vscode编辑器

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
      - /:/host-root           # 将宿主机根目录挂载到容器
    ports:
      - 8443:8443
    restart: always
```


