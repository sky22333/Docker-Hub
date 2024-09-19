### `Docker-compose`部署`drupal`网站系统

```
services:
  drupal:
    image: drupal:latest
    command: drupal
    ports:
      - "8080:80"
    depends_on:
      - db
      - redis
    environment:
      DRUPAL_DB_HOST: db
      DRUPAL_DB_NAME: drupal
      DRUPAL_DB_USER: drupal
      DRUPAL_DB_PASSWORD: drupal_password
    volumes:
      - ./drupal:/var/www/html
  db:
    image: mysql:5.7
    environment:
      MYSQL_DATABASE: drupal
      MYSQL_USER: drupal
      MYSQL_PASSWORD: drupal_password
      MYSQL_ROOT_PASSWORD: root_password
    volumes:
      - ./db:/var/lib/mysql

  redis:
    image: redis:alpine
    command: redis
    volumes:
      - ./redis:/data
```

- 进入容器
```
docker exec -it drupal /bin/sh
```
- 安装`Drush`和`redis`
```
composer require drush/drush drupal/redis
```

```
echo 'export PATH="/var/www/html/vendor/bin:$PATH"' >> ~/.bashrc
source ~/.bashrc
```
- 容器内使用命令添加配置
```
echo "
$settings['redis.connection']['interface'] = 'PhpRedis';  // 使用 PHP 的 Redis 扩展
$settings['redis.connection']['host'] = 'redis';  // Redis 容器的服务名
$settings['redis.connection']['port'] = 6379;  // Redis 默认端口

// 将默认缓存设置为 Redis
$settings['cache']['default'] = 'cache.backend.redis';

// 可选配置：禁用缓存的内部页面
$settings['cache']['bins']['render'] = 'cache.backend.redis';
$settings['cache']['bins']['dynamic_page_cache'] = 'cache.backend.redis';
$settings['cache']['bins']['page'] = 'cache.backend.redis';

// 锁定系统使用 Redis（提升性能）
$settings['container_yamls'][] = 'modules/contrib/redis/example.services.yml';

// 禁用 Drupal 内置的缓存压缩，因为 Redis 已经进行了压缩
$settings['cache']['bins']['bootstrap'] = 'cache.backend.redis';
$settings['cache']['bins']['discovery'] = 'cache.backend.redis';
" >> /var/www/html/sites/default/settings.php
```
- `drupal`容器内启用`redis`模块
```
drush en redis -y
```

- 安装并启用中文语言

```
drush pm:enable language interface_translation locale -y
drush language:add zh-hans
drush language:default zh-hans
drush locale:update
drush cache:rebuild
```



- 重启容器
```
docker compose restart drupal
```
