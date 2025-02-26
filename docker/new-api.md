### Docker部署new-api

```
services:
  one-api:
    image: calciumion/new-api
    container_name: new-api
    restart: always
    ports:
      - "3000:3000"
    environment:
      - TZ=Asia/Shanghai
    volumes:
      - ./new-api:/data
```

- 启动容器后需要等待初始化完成才能正常访问
- 初始管理员账户：`root` `123456`

### `Docker cli`命令

```
docker run --name new-api -d --restart always -p 3000:3000 -e TZ=Asia/Shanghai -v $(pwd)/new-api:/data calciumion/new-api
```
