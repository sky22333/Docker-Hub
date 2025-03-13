### Docker部署彩虹聚合DNS系统

[项目地址](https://github.com/netcccyun/dnsmgr)

创建`my.cnf`配置
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
      - ./my.cnf:/etc/mysql/my.cnf
      - ./mysql:/var/lib/mysql
    environment:
      - MYSQL_DATABASE=dnsmgr
      - MYSQL_USER=dnsmgr
      - MYSQL_PASSWORD=dnsmgr123456
      - MYSQL_ROOT_PASSWORD=dnsmgr123456
      - TZ=Asia/Shanghai
```
