# docker快速部署独角数卡

先配置npm把域名反代到`8111`端口，申请证书用`80`端口。或者caddy反代也行

[查看npm部署方法](https://github.com/sky22333/Docker-Hub/blob/main/docker/docker%20NPM.md#docker%E9%83%A8%E7%BD%B2nginx-proxy-manager)

部署独角数卡

```
services:
  mysql:
    image: mysql:5.7
    container_name: mysql
    environment:
      MYSQL_ROOT_PASSWORD: "123456"
    volumes:
      - /data/mysql/config/my.cnf:/etc/mysql/my.cnf
      - /data/mysql/db:/var/lib/mysql

  myredis:
    image: redis
    container_name: redis

  dujiaoka:
    image: jiangjuhong/dujiaoka:latest
    container_name: dujiaoka
    ports:
      - "8111:80"
      - "9000:9000"
    environment:
      - APP_URL=https://域名
      - ADMIN_HTTPS=true
      - ADMIN_ROUTE_PREFIX=/admin
      - WEB_DOCUMENT_ROOT=/app/public
    restart: always
```

输入域名进入安装页面
```

数据库地址：mysql
端口：3306
用户名：root
密码：123456

redis地址：redis
```



用户名和密码都是`admin`，`admin`

后台路径为`admin`

---
---
#### 重置密码
在容器内执行`php artisan admin:reset-password`然后输入需要重置密码的用户名，然后输入新密码

忘记管理员用户名可在数据库的`admin_users`表查看

## 高阶操作

```
docker exec -it dujiaoka /bin/bash
```
#### 修改￥标识

首页`/app/resources/views/luna/static_pages/home.blade.php`

商品页`/app/resources/views/luna/static_pages/buy.blade.php`

订单页`/app/resources/lang/zh_CN/dujiaoka.php`



#### 底部版权路径
```
/app/resources/views/luna/layouts/_footer.blade.php
```

#### 主题路径
```
/app/resources/views
```
#### css样式路径
```
/app/public/assets
```

#### 替换lnua主题背景示例

上传图片到服务器的`home`目录，把上传的图片名称改为`background.png`。

替换：
```
docker cp /home/background.png dujiaoka:/app/public/assets/luna/img/background.png
```

#### 底部运行时间代码
```
<div style="text-align: center; padding: 20px;">
    <span>本站已运行: <span id="adian_time" style="color: #000; font-weight: bold;"></span></span>
</div>

<script>
    function stime() {
        var start = new Date(2023, 9, 5, 12, 0, 0); // 月份是从0开始的，所以5月是4
        var now = new Date();
        var diff = now - start; // 时间差（毫秒）

        var msec = diff;
        var dd = Math.floor(msec / 1000 / 60 / 60 / 24);
        msec -= dd * 1000 * 60 * 60 * 24;
        var hh = Math.floor(msec / 1000 / 60 / 60);
        msec -= hh * 1000 * 60 * 60;
        var mm = Math.floor(msec / 1000 / 60);
        msec -= mm * 1000 * 60;
        var ss = Math.floor(msec / 1000);
        msec -= ss * 1000;

        var dni = dd + "天" + hh + "小时" + mm + "分" + ss + "秒";
        document.getElementById("adian_time").innerHTML = dni;
    }
    setInterval(stime, 1000); // 每秒更新时间
</script>
```
