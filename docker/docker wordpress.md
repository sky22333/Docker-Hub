###  Docker-compose部署wordpress



创建项目文件夹

```
mkdir wordpress && cd wordpress
```

创建`docker-compose.yml`文件并写入以下代码配置：


```
services:
  db:
    image: mysql:5.7
    volumes:
      - ./data/mysql:/var/lib/mysql
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: wordpressyyds
      MYSQL_DATABASE: wordpress
      MYSQL_USER: wordpress
      MYSQL_PASSWORD: wordpress
    networks:
      - wp

  wordpress:
    container_name: wordpress
    depends_on:
      - db
    image: wordpress:latest
    volumes:
      - ./data/wp:/var/www/html
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
```

运行：

```
docker compose up -d
```

站点端口为 `8000`     后台路径 `/wp-admin`

---




#### 修改docker中wordpress的上传限制。


进入 wordpress 容器
```
docker exec -it wordpress /bin/bash
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
找到这几个变量，根据自己需求修改。
```
upload_max_filesize = 2M      # PHP最大上传文件大小
post_max_size = 8M            # 服务器最大数据量和文件大小
memory_limit = 128M           # PHP内存占用限制
```
最后一步！

重启wordpress
```
docker restart wordpress
```

---

---

### 启用redis缓存的示例

```
services:
  db:
    image: mysql:5.7
    volumes:
      - ./data/mysql:/var/lib/mysql
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: wordpressyyds
      MYSQL_DATABASE: wordpress
      MYSQL_USER: wordpress
      MYSQL_PASSWORD: wordpress
    networks:
      - wp

  wordpress:
    container_name: wordpress
    depends_on:
      - db
      - redis  # 添加redis
    image: wordpress:latest
    volumes:
      - ./data/wp:/var/www/html
    ports:
      - "8000:80"
    restart: always
    environment:
      WORDPRESS_DB_HOST: db:3306
      WORDPRESS_DB_USER: wordpress
      WORDPRESS_DB_PASSWORD: wordpress
      WORDPRESS_DB_NAME: wordpress
      WORDPRESS_REDIS_HOST: redis
    networks:
      - wp

  redis:
    image: redis:alpine
    container_name: redis
    restart: always
    networks:
      - wp

networks:
  wp:
```

- 在`wp-config.php`文件中添加以下代码
```
define('WP_REDIS_HOST', 'redis');
define('WP_CACHE', true);

// 确保添加在这个代码上面
/* That's all, stop editing! Happy blogging. */
```

- 容器内安装redis扩展
```
docker exec -it wordpress /bin/bash
```
```
apt-get update && apt-get install -y libz-dev libssl-dev

pecl install redis           # 这里直接一路回车

docker-php-ext-enable redis
```
- 重启容器
```
docker restart wordpress
```

- 插件商城搜索安装插件：`Redis Object Cache` 并启用`redis`缓存
---
- 修改后台路径插件`WPS Hide Login`
- 备份插件`WPvivid`
- 直播播放器插件`SRS Player`
- 优化性能缓存插件`WP Fastest Cache`
- 压缩图片和懒加载插件`Smush`


---

### woocommerce汉化

[woocommerce汉化插件下载](https://translate.wordpress.org/locale/zh-cn/default/wp-plugins/woocommerce/) 

1：选择第一个稳定版本

2：滚动到页面底部，导出为机器对象消息目录 `.mo`文件

3：将导出的文件重命名为`woocommerce-zh_CN.mo`

4：然后上传到`wp-content/languages/plugins/`文件夹

> 或者：在`wp-content\languages`下面新建`woocommerce`目录，然后把`woocommercer-zh_CN.mo`文件放进去
