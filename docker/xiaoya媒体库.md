### 小雅媒体库

```
version: '3.8'
services:
  alist:
    image: 'xiaoyaliu/alist:latest'
    container_name: xiaoya
    environment:
      - TZ=${TZ:-Asia/Shanghai}
    volumes:
      - '/home/xiaoya:/www/data'
      - '/home/xiaoya:/data'
    ports:
      - '2346:2346'
      - '2345:2345'
      - '5678:80'
    restart: unless-stopped
```

#### 教程

配置`home/xiaoya`文件内的参数，然后重启

[配置文档](https://xiaoyaliu.notion.site/xiaoya-docker-69404af849504fa5bcf9f2dd5ecaa75f#bc572531d09e4baa80afdf3f52653c7d)

端口：5678

webdav 账号密码
用户: `guest` 密码: `guest_Api789`

重启就会自动更新数据库及搜索索引文件
```
docker restart xiaoya
```

#### 定时重启
```
crontab -e
```
输入配置
```
0 6 * * * docker restart xiaoya
```
