###  docker部署Nginx Proxy Manager


```
docker run -d \
  --name=npm \
  -p 80:80 \
  -p 81:81 \
  -p 443:443 \
  -v ./npm/data:/data \
  -v ./npm/letsencrypt:/etc/letsencrypt \
  --restart=always \
  jc21/nginx-proxy-manager:latest
```

> 中文镜像：`chishin/nginx-proxy-manager-zh:release`

面板端口为`81`


默认用户名: 
```
admin@example.com
```

默认密码: 
```
changeme
```

注意：申请证书需要用 `80` 端口，可以申请完证书再改成反代端口。



[官方文档](https://nginxproxymanager.com/advanced-config/)

---

### `docker-compose.yaml`配置

```
services:
  app:
    image: jc21/nginx-proxy-manager:latest
    container_name: npm
    restart: always
    ports:
      - '80:80'
      - '81:81'
      - '443:443'
    volumes:
      - ./data:/data
      - ./letsencrypt:/etc/letsencrypt
```
