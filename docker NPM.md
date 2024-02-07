###  docker部署Nginx Proxy Manager


```
docker run -d \
  --name=npm \
  -p 80:80 \
  -p 81:81 \
  -p 443:443 \
  -v /home/npm/data:/data \
  -v /home/npm/letsencrypt:/etc/letsencrypt \
  --restart=always \
  jc21/nginx-proxy-manager:latest
```

端口为`81`


默认用户名: 
```
admin@example.com
```

默认密码: 
```
changeme
```

注意：申请证书需要用 `80` 端口，可以申请完证书再改成反代端口。  docker内部IP一般为 `172.17.0.1` 



---
