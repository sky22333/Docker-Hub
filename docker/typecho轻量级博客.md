## Docker部署typecho



```
services:
  mysql:
    image: mysql:5.7
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: typechoPASSWORD
      MYSQL_DATABASE: typecho
      MYSQL_USER: typecho
      MYSQL_PASSWORD: typechoPASSWORD
    volumes:
      - ./mysql-data:/var/lib/mysql

  typecho:
    image: joyqi/typecho:nightly-php8.2-apache
    restart: always
    depends_on:
      - mysql
    ports:
      - "8080:80"
    environment:
      TIMEZONE: Asia/Shanghai
      TYPECHO_SITE_URL: https://your-domain.com
      TYPECHO_DB_HOST: mysql
      TYPECHO_DB_NAME: typecho
      TYPECHO_DB_USER: typecho
      TYPECHO_DB_PASS: typechoPASSWORD
    volumes:
      - ./typecho-data:/var/www/html
```

`https://your-domain.com`替换为你的网址，提前反代好。

（可选）重置后台管理员密码：删除站点目录的`config.inc.php`文件，然后重新安装选择保留原有数据库即可

#### 主题文件目录`usr/themes`

安装git:
```
sudo apt install git
```

下载主题：
```
git clone 主题Git仓库地址
```
复制到主题目录：
```
docker cp 主题文件路径 typecho-server:/app/usr/themes
```

[仓库地址](https://hub.docker.com/r/joyqi/typecho)
