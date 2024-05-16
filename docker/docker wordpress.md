###  docker-compose部署wordpress网站系统


web页面端口为 `8000`     后台路径 `wp-admin`

创建项目文件夹

```
mkdir wordpress-docker && cd wordpress-docker
```

创建`docker-compose.yml`文件并写入以下代码配置：


```
version: "3.8"

services:
  db:
    image: mysql:5.7
    volumes:
      - db_data:/var/lib/mysql
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: wordpressyyds
      MYSQL_DATABASE: wordpress
      MYSQL_USER: wordpress
      MYSQL_PASSWORD: wordpress
    networks:
        - wp

  wordpress:
    depends_on:
      - db
    image: wordpress:latest
    volumes:
      - .:/var/www/html
    ports:
      - "8000:80"
    restart: always
    environment:
      WORDPRESS_DB_HOST: db:3306
      WORDPRESS_DB_USER: wordpress
      WORDPRESS_DB_PASSWORD: wordpress
      WORDPRESS_DB_NAME: wordpress
    networks:
      - wp
networks:
  wp:
volumes:
  db_data: {}
```

运行：

```
docker-compose up -d
```


---




### 修改docker中wordpress的上传限制。

查看你当前Wordpress容器ID，命令如下：
```
docker ps
```
使用命令进入 wordpress 容器进入具体容器是使用下面的命令
```
docker exec -it 容器ID /bin/bash       //备注：替换容器ID
```
wordpress 容器中的这个路径`/usr/local/etc/php/`，是存放 `php.ini` 的地方，但是默认是没有 `php.ini` 这个文件的，所以我们要通过复制一份`php.ini-production`文件，来生成 `php.ini` 文件。
```
cd /usr/local/etc/php/
```
```
cp php.ini-production php.ini
```
然后使用vim编辑器修改即可，如果没有则需要安装一下

更新及安装vim，使用如下代码
```
apt-get update
apt-get install vim
```
安装完成vim，现在就可以对php.ini进行编辑了。
```
vim php.ini
```
修改这几个变量，根据自己需求修改。
```
upload_max_filesize = 2M      # PHP最大上传文件大小
post_max_size = 8M            # 服务器最大数据量和文件大小
memory_limit = 128M           # PHP内存占用限制
```
最后一步！

重启wordpress
```
docker restart 容器ID
```
