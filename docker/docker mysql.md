## Docker创建一个mysql 5.7数据库

#### 监听容器IP
```
docker run -d --name mysql -p 172.17.0.1:3306:3306 -v /data:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=123456 mysql:5.7
```

#### 使用docker host网络模式
```
docker run -d --name mysql --network=host -v /data:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=123456 mysql:5.7
```


#### 连接到指定容器网络
```
docker run -d --name mysql --network my_network -v /data:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=123456 mysql:5.7
```

## 数据库管理

```
docker pull phpmyadmin/phpmyadmin
```

```
docker run --name my-phpmyadmin -d -p 7890:80 phpmyadmin/phpmyadmin
```

环境变量：
```
-p 7890:80                 // 映射面板的端口。
-e PMA_HOST=172.17.0.1     // 指定 phpMyAdmin 连接到的数据库主机地址。
-e PMA_PORT=3306           // 指定 MySQL 数据库的端口号。
-e PMA_USER=root           // 指定登录数据库的用户名。
-e PMA_PASSWORD=123456     // 指定登录数据库的密码。
--network my_network       // 指定容器网络
```

#### docker-compose.yaml
```
services:
  phpmyadmin:
    image: phpmyadmin/phpmyadmin
    container_name: my-phpmyadmin
    ports:
      - "7890:80"
    environment:
      - UPLOAD_LIMIT=64M
      - POST_MAX_SIZE=64M
      - MEMORY_LIMIT=128M
    networks:
      - my_network

networks:
  my_network:
    external: true
```


---

#### 查看容器网络
```
docker inspect -f '{{.HostConfig.NetworkMode}}' 容器名称或ID
```



---

