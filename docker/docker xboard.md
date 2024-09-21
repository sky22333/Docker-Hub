## Docker-Compose 部署Xboard面板


### [原项目地址](https://github.com/cedar2025/Xboard)

### [备用项目](https://github.com/admin8800/Xboard)

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

订阅下发文件目录
```
/www/resources/rules/custom.clash.yaml
```
https://github.com/cedar2025/Xboard/tree/5a0e59b103657ccd300204046b877f653cd2aa30/app/Protocols


强制获取订阅（URL后面加上这个参数）
```
&flag=meta&types=all
```

忘记管理员密码可以在站点目录下执行命令找回密码
```
docker exec -it xboard-xboard-1 /bin/sh
```
```
php artisan reset:password 管理员邮箱
```
重启
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



## 迁移
```
# 备份现有数据库
mysqldump -u root -p --databases my_database > my_database_backup.sql

# 登录 MySQL
mysql -u root -p

# 删除现有数据库
DROP DATABASE my_database;

# 创建新数据库
CREATE DATABASE my_database;

# 退出 MySQL
exit;

# 恢复备份
mysql -u root -p my_database < my_database_backup.sql
```

然后进入数据库的`v2_settings`表，修改https配置，域名配置，路径配置，即可正常进入后台


---
---

### Xboard将reCAPTCHA替换为Cloudflare Turnstile的方法

1.修改 `/vendor/google/recaptcha/src/ReCaptcha/ReCaptcha.php`
 配置中的`SITE_VERIFY_URL`为`https://challenges.cloudflare.com/turnstile/v0/siteverify`

2.管理后台【主题配置】— 主题设置增加：
```
<script src="https://challenges.cloudflare.com/turnstile/v0/api.js?compat=recaptcha" async defer></script>
```

3.cloudflare turnstile 增加Turnstile站点获取密钥。然后更改管理后台密钥



### 邮件设置：

 [微软outlook邮箱教程](https://garden.1900.live/22-knowledge/%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F/windows/%E5%BC%80%E5%90%AF%E5%BE%AE%E8%BD%AF-outlook-%E9%82%AE%E7%AE%B1-pop-imap-smtp-%E6%9C%8D%E5%8A%A1%E5%92%8C%E8%8E%B7%E5%8F%96%E6%9C%8D%E5%8A%A1%E5%AF%86%E7%A0%81-%E6%8E%88%E6%9D%83%E7%A0%81)

SMTP服务器地址：谷歌邮箱`smtp.gmail.com`， 163邮箱`smtp.163.com`， 雅虎邮箱`smtp.mail.yahoo.com`。

SMTP 端口：465 (SSL)/587 (TLS)

SMTP密码：谷歌搜索`Gmail邮箱获取SMTP密码教程`

打开Gmail邮箱——点击设置——点击查看所有设置——点击转发和 POP/IMAP——启用 IMAP——点击保存更改——点击继续

点击管理你的google账号——点击安全性——点击两步验证——开启两步验证——点开两步验证——拉到最下面——点击应用专用密码——应用选择其他——自定义一个名称——点击生成
