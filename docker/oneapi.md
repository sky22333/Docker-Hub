### Docker部署oneapi

```
services:
  one-api:
    image: justsong/one-api
    container_name: one-api
    restart: always
    ports:
      - "3000:3000"
    environment:
      - TZ=Asia/Shanghai
    volumes:
      - ./one-api:/data
```

或者

```
docker run --name one-api -d --restart always -p 3000:3000 -e TZ=Asia/Shanghai -v $(pwd)/one-api:/data justsong/one-api
```
