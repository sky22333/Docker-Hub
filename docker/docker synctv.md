## 直播和观影平台synctv

```
docker run -d --name synctv -p 8080:8080 synctvorg/synctv:latest
```

默认管理员账户：
```
root
```
```
root
```

[官方文档](https://synctv.wiki/#/zh-cn/quickstart)



如果你想改前端文件，需要clone前端项目，然后修改，编译出前端文件

然后运行docker的时候映射编译好的前端文件：`-v /路径/dist:/dist`，同时添加环境变量`-e WEB_PATH=/dist`


例如：
```
docker run -d --name synctv -p 8080:8080 -e WEB_PATH=/dist -v /路径/dist:/dist synctvorg/synctv:latest
```

## 相关代码目录
```
更改用户权限目录：src/types/User.ts

更改首页文本和站点名称：src/components/Header.vue        # 95行可去掉logo灰色背景

更改首页logo图片：src/assets/appIcons/synctv-nobg.svg

更改底部版权：src/views/HomeView.vue

更改浏览器地址栏logo图标：public/favicon.svg

更改房间页面样式：src/components/cinema/RoomInfo.vue        # 可删除分享链接模块，防止分享链接带密码
```

#### 后端改为游客可以发言
```
git clone https://github.com/synctv-org/synctv.git
```

后端项目`internal/op/room.go`目录的256行和287行`=`号后面改为`model.NoPermission | model.PermissionSendChatMessage`，然后编译docker镜像运行

```
docker build -t tv .
```
```
docker run -d --name tv -p 8080:8080 -e WEB_PATH=/dist -v 文件路径:/dist tv
```
