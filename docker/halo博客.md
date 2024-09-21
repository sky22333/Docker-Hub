### Docker部署halo博客

```
docker run -it -d --name halo -p 8090:8090 -v ./halo2:/root/.halo2 halohub/halo:2
```



地址： `IP:8090`

后台路径：`/console`


---

***国内阿里云镜像***
```
registry.fit2cloud.com/halo/halo
```

---

### `docker-compose.yaml`部署

```
services:
  halo:
    image: halohub/halo:2
    restart: always
    ports:
      - "8090:8090"
    volumes:
      - ./halo2:/root/.halo2
    command:
      - --spring.r2dbc.url=r2dbc:pool:mysql://mysql:3306/halo
      - --spring.r2dbc.username=halo                      
      - --spring.r2dbc.password=halo_password
      - --spring.sql.init.platform=mysql
      - --halo.external-url=http://IP:8090/
      - --server.port=8090

    mysql:
      image: mysql:5.7
      container_name: mysql
      environment:
        MYSQL_DATABASE: halo                  # 数据库名称
        MYSQL_USER: halo                      # 数据库用户名
        MYSQL_PASSWORD: halo_password         # 数据库密码
        MYSQL_ROOT_PASSWORD: halo_password    # 数据库root密码
      volumes:
        - ./data/mysql:/var/lib/mysql         # 映射数据库文件
      restart: always
```
