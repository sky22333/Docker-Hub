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

更改logs图片：src/assets/appIcons/synctv-nobg.svg

更改底部版权：src/views/HomeView.vue

更改浏览器地址栏图标：public/favicon.svg

更改房间页面样式：src/components/cinema/RoomInfo.vue        #  可删除分享链接元素，防止分享链接带密码
```
