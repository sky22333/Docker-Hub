### docker部署flarum论坛网站

- 创建项目文件
```
mkdir -p flarum && cd flarum && touch docker-compose.yaml flarum.env
```

- `docker-compose.yaml`配置
  
```
services:
  flarum:
    image:  mondedie/flarum:latest
    container_name: flarum
    env_file:
      - ./flarum.env   # 站点配置文件
    volumes:
      - ./flarum/assets:/flarum/app/public/assets
      - ./flarum/extensions:/flarum/app/extensions

    ports:
      - 8888:8888
    restart: always
    depends_on:
      - mariadb

  mariadb:
    image: mariadb:10.5
    container_name: mariadb
    environment:
      - MYSQL_ROOT_PASSWORD=abc123456
      - MYSQL_DATABASE=flarum
      - MYSQL_USER=flarum
      - MYSQL_PASSWORD=abc123456
    volumes:
      - ./mysql:/var/lib/mysql  # 数据映射到本地
    restart: always
```


- `flarum.env`配置

```
DEBUG=false
FORUM_URL=https://xxxxxxxxx.com     # 论坛域名

# 数据库信息
DB_HOST=mariadb
DB_NAME=flarum
DB_USER=flarum
DB_PASS=abc123456
DB_PREF=flarum_
DB_PORT=3306

# 站点环境变量
FLARUM_ADMIN_USER=admin                  # 管理员用户名
FLARUM_ADMIN_PASS=admin123456            # 管理员密码
FLARUM_ADMIN_MAIL=admin@admin.com        # 管理员邮箱
FLARUM_TITLE=Test flarum                 # 论坛标题
```


- 启动

```
docker compose up -d mariadb      # 必须先启动数据库，等待数据库初始化完成

docker compose up -d flarum       # 启动站点
```


- 安装插件

查看所有插件
```
docker exec -ti flarum extension list
```
删除插件
```
docker exec -ti flarum extension remove 作者/插件
```
中文语言
```
docker exec -ti flarum extension require flarum-lang/chinese-simplified:dev-master
```
私信功能插件
```
docker exec -ti flarum extension require neoncube/flarum-private-messages:"*"
```

HubUi-X主题
```
docker exec -ti flarum extension require kk14569/flarum-hubui-x
```



> 官网-[更多主题](https://flarum.org/extensions?tableSortColumn=downloads&tableSortDirection=desc)


