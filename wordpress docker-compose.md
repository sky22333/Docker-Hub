###  docker-compose部署wordpress博客系统


```
mkdir wordpress-docker && cd wordpress-docker
```
```
nano docker-compose.yml
```


```
version: "3.9"

services:
  db:
    image: mysql:5.7
    volumes:
      - db_data:/var/lib/mysql
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: somewordpress
      MYSQL_DATABASE: wordpress
      MYSQL_USER: wordpress
      MYSQL_PASSWORD: wordpress
    networks:
        - wp

  wordpress:
    depends_on:
      - db
    image: wordpress:latest
    volumes:
      - .:/var/www/html
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
volumes:
  db_data: {}
```



```
docker-compose up -d
```


---
