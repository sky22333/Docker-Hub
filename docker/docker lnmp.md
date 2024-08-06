### docker部署LNMP环境

```
cd /home && touch docker-compose.yml nginx.conf
```

```
version: '3.8'

services:
  nginx:
    image: nginx
    container_name: nginx
    restart: always
    ports:
      - "80:80"
      - "443:443"
    volumes:
      - ./nginx.conf:/etc/nginx/nginx.conf
      - ./conf.d:/etc/nginx/conf.d
      - ./certs:/etc/nginx/certs
      - ./html:/var/www/html
      - ./log/nginx:/var/log/nginx


  php:
    image: php:8.0-fpm
    container_name: php
    restart: always
    volumes:
      - ./html:/var/www/html

  mysql:
    image: mysql
    container_name: mysql
    restart: always
    volumes:
      - ./mysql:/var/lib/mysql
    environment:
      MYSQL_ROOT_PASSWORD: mysql666
      MYSQL_USER: mysql
      MYSQL_PASSWORD: mysql666

  redis:
    image: redis
    container_name: redis
    restart: always
    volumes:
      - ./redis:/data
```


```
docker compose up -d
```




---
