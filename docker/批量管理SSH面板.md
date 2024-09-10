### Docker部署easynode批量管理SSH面板

```
docker run -d \
  --net=host \
  --restart=always \
  -v /root/easynode/db:/easynode/app/db \
  chaoszhu/easynode
```

默认信息
```
默认账户密码 admin/admin
web端口：8082
```

- `docker-compise.yaml`配置

```
services:
  easynode:
    image: chaoszhu/easynode
    container_name: easynode
    restart: always
    network_mode: host
    volumes:
      - ./easynode/db:/easynode/app/db
```

变量：`-e ALLOWED_IPS=127.0.0.1,127.0.0.2` 指定IP白名单才能访问



---

[项目地址](https://github.com/chaos-zhu/easynode)
