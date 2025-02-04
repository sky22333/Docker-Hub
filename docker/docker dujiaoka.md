# docker快速部署独角数卡

- 先把域名反代到`8111`端口并开启HTTPS

**Docker-compose配置：**
```
services:
  mysql:
    image: mysql:5.7
    environment:
      MYSQL_DATABASE: dujiaoka                  # 数据库名称
      MYSQL_USER: dujiaoka                      # 数据库用户名
      MYSQL_PASSWORD: dujiaoka_password         # 数据库密码
      MYSQL_ROOT_PASSWORD: dujiaoka_password    # 数据库root密码
    volumes:
      - ./data/mysql:/var/lib/mysql             # 映射数据库文件
    restart: always

  redis:
    image: redis:alpine
    restart: always

  dujiaoka:
    image: jiangjuhong/dujiaoka:latest
    container_name: dujiaoka
    ports:
      - "8111:80"
    environment:
      - APP_URL=https://域名                     # 替换域名
      - ADMIN_HTTPS=true
      - ADMIN_ROUTE_PREFIX=/admin
      - WEB_DOCUMENT_ROOT=/app/public
      - TZ=Asia/Shanghai
    restart: always
```

输入域名进入安装页面
```

数据库地址：mysql
端口：3306
数据库名称：dujiaoka
用户名：dujiaoka
密码：dujiaoka_password

redis地址：redis
```



用户名和密码都是`admin`，`admin`

后台路径为`admin`

---
---

### 支付配置
易支付对接地址示例：`http://xxxxx.com/submit.php`

epusdt对接示例：`http://127.0.0.1:8000/api/v1/order/create-transaction`，同一台服务器可以填本地IP


> 易支付改不跳支付方式：`app/Http/Controllers/Pay/YipayController.php`去掉第`19`行。


## 进入容器

```
docker exec -it dujiaoka /bin/sh
```

#### 重置密码
在容器内执行`php artisan admin:reset-password`然后输入需要重置密码的用户名，然后输入新密码

忘记管理员用户名可在数据库的`admin_users`表查看

---

#### 修改`¥`标识

hyper：`resources/lang/zh_CN/hyper.php`第`11`行

unicorn：`resources/lang/zh_CN/dujiaoka.php`第`91`行

首页`/app/resources/views/lang/static_pages/home.blade.php`

商品页`/app/resources/views/lang/static_pages/buy.blade.php`

订单页`/app/resources/lang/zh_CN/dujiaoka.php`

---

#### 下单邮箱改为不用必填

1：`app/Service/OrderService.php`文件第`62`行，将`required`改为`nullable`

2：`app/Http/Controllers/Home/OrderController.php`第`77`行删除，替换为以下代码

> 如果`$email`为空，生成一个随机邮箱
```
$email = $request->input('email');
if (empty($email)) {
    $randomString = bin2hex(random_bytes(5));
    $email = $randomString . uniqid() . '@aa.com';
}
$this->orderProcessService->setEmail($email);
```

3：`resources/views/unicorn/static_pages/buy.blade.php`文件第`63`行，去掉`required`代码，`luna`在`126`行，`hyper`则需要删除第`176`行的整个判断。

> 如果要留空则需要数据库修改`email`表结构。

---

#### 底部版权路径
```
resources/views/unicorn/layouts/_footer.blade.php
```

#### 主题路径
```
/app/resources/views
```
#### css样式路径
```
/app/public/assets
```
主题背景路径
```
/app/public/assets/luna/img/
```

---

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
