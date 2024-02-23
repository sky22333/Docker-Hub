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
      - 800:80 # 左边为主机端口,可以修改.
    container_name: maccms
    volumes:
      - ./cms:/var/www/html  # 将容器内的 /var/www/html 目录映射到宿主机的 ./cms 目录
  # mysql数据库
  db:
    image: mysql:5.7
    restart: always
    environment:
      - MYSQL_ROOT_PASSWORD=admin@ADMIN  # 数据库密码，可以修改
    container_name: maccms-mysql
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

文件已经映射到本地，模板文件在`/root/cms/cms/template`目录

先停止容器
```
docker stop maccms
```

然后把模板文件上传到`/root/cms/cms/template`目录下

启动容器
```
docker start maccms
```

回到浏览器，ctl+f5强制刷新页面，就能看到模板替换成功了


---
---

#### [萌芽采集插件](https://www.mycj.pro/mycj-down)https://www.mycj.pro/mycj-down
