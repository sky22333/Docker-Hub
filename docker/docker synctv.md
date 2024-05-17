### 直播和观影平台synctv

```
docker run -d --name synctv -p 8080:8080 synctvorg/synctv
```

默认管理员账户：
```
root
```
```
root
```

[官方文档](https://synctv.wiki/#/zh-cn/quickstart)



如果你想改前端文件，需要clone前端项目，然后修改编译出前端文件，然后映射到docker内部比如: `-v dist:/root/dist`，然后运行项目的时候加上启动参数 `--web-path /root/dist` 就行
