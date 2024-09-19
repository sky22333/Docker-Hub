### `Docker-compose`部署`wyx2685/v2board`


```
services:
  v2board:
    image: markfen/v2board-wyx2865:latest
    depends_on:
      - mysql
    ports:
      - "8080:80"
    volumes:
      - ./v2board:/usr/local/src
    networks:
      - v2board
    restart: always

  mysql:
    image: mysql:5.7
    container_name: mysql
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: v2board7890      # root 用户的密码
      MYSQL_DATABASE: v2board               # 数据库名称
      MYSQL_USER: v2board                   # 数据库用户名
      MYSQL_PASSWORD: v2board7890           # 用户的密码
    volumes:
      - ./mysql:/var/lib/mysql
    networks:
      - v2board

networks:
  v2board:
```
- 进入容器执行安装命令
```
docker exec -it v2board /bin/sh

cd /usr/share/nginx/html/v2board && && rm -rf .env && sh /usr/share/nginx/html/v2board/init.sh
```


- 授予 v2board 权限
```
chmod -R 777 /usr/share/nginx/html/v2board
```

- 重新启动队列
```
supervisorctl restart v2board
```

***部署完成*** 

 

- 当需要重新部署环境时，只需要删除
```
cd /usr/share/nginx/html/v2board && rm -rf .env
```
