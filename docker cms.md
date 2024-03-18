## 使用docker-compose部署苹果cms

#### 创建相关文件

```
mkdir /root/cms ; cd /root/cms && touch docker-compose.yml
```

#### 编辑`docker-compose.yml`配置

```
version: '3.3'
services:
  # 苹果cms
  maccms:
    depends_on:
      - db
    image: esme518/docker-maccms10  # 使用 esme518/docker-maccms10 最新镜像
    restart: always
    ports:
      - 172.17.0.1:800:80 # 左边为主机端口,可以修改.
    container_name: maccms
    volumes:
      - ./cms:/var/www/html  # 将数据映射到 /root/cms 目录
  # mysql数据库
  db:
    image: mysql:5.7
    restart: always
    environment:
      - MYSQL_ROOT_PASSWORD=admin@ADMIN  # 数据库密码，可以修改
    container_name: maccms-mysql
```
若要共用数据库请替换数据库部分
```
    environment:
      - DB_HOST=172.17.0.1
      - DB_NAME=mysql
      - DB_USER=root
      - DB_PASSWORD=123456
```

#### 运行
```
docker-compose up -d
```


#### 进入安装页面

```
启动成功后，浏览器输入ip:800端口就进入了系统安装界面

服务器地址：db
数据库端口：3306
数据库名称：maccms
数据库账号：root
数据库密码：在docker-cmopose.yml设置的数据库的密码
```


然后浏览器输入`http://你的ip:800/cmsadmin.php`进入站点后台


#### 更换模板

文件已经映射到本地，把模板文件放在`/root/cms/cms/template`目录



---
---

#### [图图采集插件](https://maccmsbox.com/details416.html)

#### [萌芽采集插件](https://www.mycj.pro/mycj-down)

下载插件上传到网站根目录，并使用`unzip`命令解压

赋予权限`addons/mycj`目录下的全部权限

然后后台应用商城刷新并启用

#### [免费模板推荐](https://www.maccmsbox.com/)

模板文件放在`template`目录，有的模板需要直接放在网站根目录
