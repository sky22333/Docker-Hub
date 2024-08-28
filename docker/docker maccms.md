## 使用docker-compose部署苹果cms

#### 创建相关文件

```
mkdir /root/cms ; cd /root/cms && touch docker-compose.yml
```

#### 编辑`docker-compose.yml`配置

```
services:
  maccms:
    depends_on:
      - mysql
    image: esme518/docker-maccms10:latest
    restart: always
    ports:
      - "8090:80"
    container_name: maccms
    volumes:
      - ./cms:/var/www/html
    networks:
      - cms_network

  mysql:
    image: mysql:5.7
    container_name: mysql
    restart: always
    environment:
      - MYSQL_ROOT_PASSWORD=123456
    volumes:
      - /data/mysql:/var/lib/mysql
    networks:
      - cms_network

networks:
  cms_network:
```


#### 运行
```
docker compose up -d
```


#### 进入安装页面

```
启动成功后，浏览器输入ip:8090端口就进入了系统安装界面

数据库地址：mysql
数据库端口：3306
数据库名称：mysql
数据库账号：root
数据库密码：123456
```


然后浏览器输入`http://你的ip:8090/cmsadmin.php`进入站点后台


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
