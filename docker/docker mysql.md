## Docker创建mysql数据库

#### 监听容器IP
```
docker run -d --name mysql -p 172.17.0.1:3306:3306 -v ./data:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=123456 mysql:5.7
```

#### 使用docker host网络模式
```
docker run -d --name mysql --network=host -v ./data:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=123456 mysql:5.7
```


#### 连接到指定容器网络
```
docker run -d --name mysql --network my_network -v ./data:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=123456 mysql:5.7
```

## 数据库管理

```
docker run --name phpmyadmin -d -p 7890:80 phpmyadmin:latest
```

环境变量：
```
-p 7890:80                 // 映射面板的端口。
-e PMA_HOST=172.17.0.1     // 指定连接到的数据库地址。
-e PMA_PORT=3306           // 指定 MySQL 数据库的端口号。
-e PMA_USER=root           // 指定登录数据库的用户名。
-e PMA_PASSWORD=123456     // 指定登录数据库的密码。
-e PMA_DATABASE=mysqldb    // 默认连接的数据库名称
--network my_network       // 指定容器网络
```

#### `docker-compose.yaml`示例连接到指定数据库示例
```
services:
  phpmyadmin:
    image: phpmyadmin:latest
    container_name: phpmyadmin
    ports:
      - "7890:80"
    environment:
      # 文件上传和内存限制设置
      - UPLOAD_LIMIT=512M            # 设置上传文件大小限制      
      # 数据库连接设置
      - PMA_HOST=mysql                    # MySQL 地址
      - PMA_PORT=3306                     # MySQL 端口号
      - PMA_DATABASE=acgfakadb            # 默认连接的数据库名称
    
    networks:
      - home_default        # 连接到指定网络

networks:
  home_default:
    external: true
```


---

#### 查看容器网络
```
docker inspect -f '{{.HostConfig.NetworkMode}}' 容器名称或ID
```



---

### 定时自动备份
支持`mysql`和`mariadb`数据库
```
services:
  backup-db:
    image: fradelg/mysql-cron-backup
    container_name: backup-db
    environment:
      - MYSQL_HOST=mysql            # 数据库地址
      - MYSQL_PORT=3306             # 数据库端口
      - MYSQL_USER=db_user          # 数据库用户名
      - MYSQL_PASS=db_password      # 数据库密码
      - MYSQL_DATABASE=db_name      # 数据库名称
      - MAX_BACKUPS=10             # 保留的备份数量，旧的备份将被清理
      - CRON_TIME=0 3 * * *        # 每天凌晨3点自动执行备份
    volumes:
      - ./backup:/backup          # 挂载本地目录用于保存备份
    restart: always

    networks:
      - home_default  # 连接到指定网络

networks:
  home_default:
    external: true
```

恢复备份的数据库
```
docker container exec backup-db /restore.sh ./backup/<your_sql_backup_gz_file>
```
>恢复当前目录内的`./backup/<your_sql_backup_gz_file>`文件，需替换具体文件名称
>
>如果恢复成功，会输出`Restore succeeded`  否则会输出`Restore failed`
>
>添加变量`- INIT_BACKUP=1`则在容器启动时立即创建备份
>
>如果要使用宿主机网络则移除网络配置，增加`network_mode: host`配置


[定时备份PostgresSQL数据库](https://github.com/prodrigestivill/docker-postgres-backup-local)
