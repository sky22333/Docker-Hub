## Docker-Compose 部署Xboard面板


### [项目地址](https://github.com/cedar2025/Xboard)

### [部署教程](https://github.com/cedar2025/Xboard/blob/dev/docs/docker-compose%E5%AE%89%E8%A3%85%E6%8C%87%E5%8D%97.md)

#### [防机器人验证](https://www.google.com/recaptcha/admin/create)
---

主题目录
```
public/theme
```

支付插件目录
```
app/Payments
```

客户端适配参考目录
```
app/Http/Controllers/Client/Protocols
```

忘记管理员密码可以在站点目录下执行命令找回密码
```
docker exec -it xboard-xboard-1 /bin/sh
```
```
php artisan reset:password 管理员邮箱
```
更改后台路径后需重启
```
cd /root/Xboard
```
```
docker compose restart
```
---

###  订阅被墙解决办法：

使用此方法后机场官网可以Cloudflare上打开质询，减少被墙几率和攻击几率。

订阅链接和机场官网分成两个域名，将订阅域名绑定到Cloudflare上，解析IP为你的机场原始IP，建议打开云朵。
然后添加以下配置，可以防止订阅地址被解析为机场网站，从而防止被墙。
![alt](/png/jichangurl.png)




---


---

[国旗图标](https://www.emojiall.com/zh-hans/sub-categories/J2)

---



##### 主题配置——主题设置——自定义页脚

```
<!DOCTYPE html>
<html>
<head>
    <title>Custom Animated Button</title>
    <style>
        @keyframes shake {
            0%, 100% { transform: translateX(0); }
            10%, 30%, 50%, 70%, 90% { transform: translateX(-10px); }
            20%, 40%, 60%, 80% { transform: translateX(10px); }
        }

        .tg-service-button {
            position: fixed;
            right: 20px;
            bottom: 20px;
            padding: 6px 16px; /* Further reduced padding */
            background-color: rgba(255, 255, 255, 0.7); /* 70% transparency */
            color: black; /* Black text */
            font-weight: bold; /* Bold font */
            border: 2px solid black; /* Black border */
            border-radius: 10px;
            cursor: pointer;
            font-size: 14px; /* Reduced font size */
            text-align: center;
            box-shadow: 0 0 8px black; /* Black glow effect */
        }
    </style>
</head>
<body>

    <button class="tg-service-button" onclick="redirectToTelegram()">交流群</button>

    <script>
        function redirectToTelegram() {
            window.open('https://t.me', '_blank');
        }

        // Start shaking after every 6 seconds, shake for 1 second
        setInterval(function() {
            let button = document.querySelector('.tg-service-button');
            button.style.animation = 'shake 1s';
            setTimeout(function() {
                button.style.animation = 'none';
            }, 1000);
        }, 6000);
    </script>

</body>
</html>
```
