### 使用docker安装1panel面板

### Docker方式安装
```
docker run -d \
    --name 1panel \
    --restart always \
    --network host \
    -v /var/run/docker.sock:/var/run/docker.sock \
    -v /var/lib/docker/volumes:/var/lib/docker/volumes \
    -v /opt:/opt \
    -v /root:/root \
    -e TZ=Asia/Shanghai \
    moelin/1panel:latest
```

***
- 默认端口：`10086`
- 默认账户：`1panel`
- 默认密码：`1panel_password`
- 默认入口：`entrance`
***
- 不可调整参数
  - `/var/run/docker.sock`的相关映射
 ***
- 可调整参数
> **推荐使用/opt路径，否则有些调用本地文件的应用可能出现异常**
  - `/opt:/opt`                        文件存储映射
  - `TZ=Asia/Shanghai`                        时区设置
  - `1panel`                          容器名
  - `/var/lib/docker/volumes:/var/lib/docker/volumes` 存储卷映射
***



### Docker-compose方式安装

创建一个`docker-compose.yaml`文件
```
services:
  1panel:
    container_name: 1panel # 容器名
    restart: always
    network_mode: "host"
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
      - /var/lib/docker/volumes:/var/lib/docker/volumes
      - /opt:/opt  # 文件存储映射
      - /root:/root  # 可选的文件存储映射
    environment:
      - TZ=Asia/Shanghai  # 时区设置
    image: moelin/1panel:latest
    labels:  
      createdBy: "Apps"
```

然后`docker compose up -d`运行


---

[项目地址](https://github.com/okxlin/docker-1panel)
