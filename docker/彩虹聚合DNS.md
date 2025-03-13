### Docker部署彩虹聚合DNS系统

[项目地址](https://github.com/netcccyun/dnsmgr)

创建`mysql/my.cnf`配置
```
[mysqld]
sql_mode=STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION
```

`docker-compose.yml`
```
services:
  dnsmgr:
    image: netcccyun/dnsmgr
    container_name: dnsmgr
    ports:
      - 8080:80
    volumes:
      - ./dnsmgr/web:/app/www
    depends_on:
      - mysql

  mysql:
    image: mysql:5.7
    container_name: mysql
    restart: always
    volumes:
      - ./mysql/my.cnf:/etc/mysql/my.cnf
      - ./mysql/data:/var/lib/mysql
    environment:
      - MYSQL_DATABASE=dnsmgr
      - MYSQL_USER=dnsmgr
      - MYSQL_PASSWORD=dnsmgr123456
      - MYSQL_ROOT_PASSWORD=dnsmgr123456
      - TZ=Asia/Shanghai
```


登陆mysql容器创建数据库
```
docker exec -it mysql sh
mysql -uroot -pdnsmgr123456
create database dnsmgr;
```

然后再访问页面初始化站点
