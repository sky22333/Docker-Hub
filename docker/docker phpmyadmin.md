## 数据库管理

```
docker pull phpmyadmin/phpmyadmin
```

```
docker run --name my-phpmyadmin -d -p 7890:80 phpmyadmin/phpmyadmin
```

环境变量：
```
-p 7890:80                 // 映射容器的 80 端口到主机的 7890 端口。
-e PMA_HOST=172.17.0.1     // 指定 phpMyAdmin 连接到的数据库主机地址。
-e PMA_PORT=3306           // 指定 MySQL 数据库的端口号。
-e PMA_USER=root           // 指定登录数据库的用户名。
-e PMA_PASSWORD=123456     // 指定登录数据库的密码。
--network wordpress        // 指定容器网络
```
