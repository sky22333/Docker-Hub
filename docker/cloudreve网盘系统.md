### Docker-compose安装私人网盘Cloudreve

创建项目文件夹
```
mkdir -p cloudreve && cd cloudreve && touch docker-compose.yaml
```
创建配置文件
```
mkdir -vp cloudreve/{uploads,avatar} \
&& touch cloudreve/conf.ini \
&& touch cloudreve/cloudreve.db \
&& mkdir -p aria2/config \
&& mkdir -p data/aria2 \
&& chmod -R 777 data/aria2
```

`docker-compose.yaml`配置

```
services:
  cloudreve:
    container_name: cloudreve
    image: cloudreve/cloudreve:latest
    restart: unless-stopped
    ports:
      - "5212:5212"      # 映射端口
    volumes:
      - temp_data:/data
      - ./cloudreve/uploads:/cloudreve/uploads
      - ./cloudreve/conf.ini:/cloudreve/conf.ini
      - ./cloudreve/cloudreve.db:/cloudreve/cloudreve.db
      - ./cloudreve/avatar:/cloudreve/avatar
    depends_on:
      - aria2
  aria2:
    container_name: aria2
    image: p3terx/aria2-pro
    restart: unless-stopped
    environment:
      - RPC_SECRET=aria_rpc_token-asjSADKKMkdhasjk67788    # 随意设置个强密码
      - RPC_PORT=6800
    volumes:
      - ./aria2/config:/config
      - temp_data:/data
volumes:
  temp_data:
    driver: local
    driver_opts:
      type: none
      device: $PWD/data
      o: bind
```

```
docker compose up -d
```

查看默认管理员信息
```
docker logs cloudreve
```



---
> [官方文档](https://docs.cloudreve.org/manage/db-script)



---

### `nextcloud`网盘系统

```
services:
  mysql:
    image: mysql:5.7
    container_name: mysql
    environment:
      MYSQL_ROOT_PASSWORD: nextcloud_password
      MYSQL_DATABASE: nextcloud
      MYSQL_USER: nextcloud
      MYSQL_PASSWORD: nextcloud_password
    volumes:
      - ./mysql-data:/var/lib/mysql
    restart: always

  nextcloud:
    image: nextcloud
    container_name: nextcloud
    ports:
      - "8080:80"
    volumes:
      - ./nextcloud:/var/www/html  # 文件存储目录
    environment:
      MYSQL_PASSWORD: nextcloud_password
      MYSQL_DATABASE: nextcloud
      MYSQL_USER: nextcloud
      MYSQL_HOST: mysql
    depends_on:
      - mysql
    restart: always
```
