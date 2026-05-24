### Docker-Compose 部署Xboard面板


- [官方github](https://github.com/cedar2025/Xboard)

- [第三方](https://github.com/admin8800/Xboard)



####  订阅被墙解决办法：

使用此方法后机场官网可以Cloudflare上打开质询，减少被墙几率和攻击几率。

订阅链接和机场官网分成两个域名，将订阅域名绑定到Cloudflare上，解析IP为你的机场原始IP，建议打开云朵。
然后添加以下配置，可以防止订阅地址被解析为机场网站，从而防止被墙。
![alt](/png/jichangurl.png)


---


---

[国旗图标](https://www.emojiall.com/zh-hans/sub-categories/J2)

---


#### 迁移
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

#### 内置数据库路径
```
.docker/.data/database.sqlite
```

---

#### Xboard将reCAPTCHA替换为Cloudflare Turnstile的方法

1.修改 `/vendor/google/recaptcha/src/ReCaptcha/ReCaptcha.php`
 配置中的`SITE_VERIFY_URL`为`https://challenges.cloudflare.com/turnstile/v0/siteverify`

2.管理后台【主题配置】— 主题设置增加：
```
<script src="https://challenges.cloudflare.com/turnstile/v0/api.js?compat=recaptcha" async defer></script>
```

3.cloudflare turnstile 增加Turnstile站点获取密钥。然后更改管理后台密钥
