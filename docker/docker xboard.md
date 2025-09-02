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
修改支付回调返回的URL
```
app/Services/PaymentService.php
```
客户端适配参考目录
```
app/Http/Controllers/Client/Protocols
```


已购买用户不显示订阅套餐：
```
app/Services/PlanService.php
```
注释过滤部分
```
public function getAvailablePlans(): `Collection`
    {
        return Plan::where('show', true)
            ->where('sell', true)
            ->orderBy('sort')
            ->get();
//            ->filter(function ($plan) {
//                return $this->hasCapacity($plan);
//            });
    }
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


## XBoard API文档

<details>
  <summary>XBoard API列表</summary>

## V1 版本 API

### 1. 客户端相关 (Client)
- `GET /client/subscribe` - 客户端订阅
- `GET /client/app/getConfig` - 获取应用配置
- `GET /client/app/getVersion` - 获取应用版本

### 2. 访客相关 (Guest)
- `GET /guest/plan/fetch` - 获取套餐信息
- `POST /guest/telegram/webhook` - Telegram webhook
- `GET|POST /guest/payment/notify/{method}/{uuid}` - 支付通知
- `GET /guest/comm/config` - 获取通用配置

### 3. 认证相关 (Passport)
- `POST /passport/auth/register` - 用户注册
- `POST /passport/auth/login` - 用户登录
- `GET /passport/auth/token2Login` - Token 登录
- `POST /passport/auth/forget` - 忘记密码
- `POST /passport/auth/getQuickLoginUrl` - 获取快速登录 URL
- `POST /passport/auth/loginWithMailLink` - 邮件链接登录
- `POST /passport/comm/sendEmailVerify` - 发送邮箱验证
- `POST /passport/comm/pv` - 页面浏览统计

### 4. 服务器相关 (Server)
#### UniProxy
- `GET /server/UniProxy/config` - 获取配置
- `GET /server/UniProxy/user` - 获取用户
- `POST /server/UniProxy/push` - 推送数据
- `POST /server/UniProxy/alive` - 存活检测
- `GET /server/UniProxy/alivelist` - 存活列表
- `POST /server/UniProxy/status` - 状态更新

#### ShadowsocksTidalab
- `GET /server/ShadowsocksTidalab/user` - 获取用户
- `POST /server/ShadowsocksTidalab/submit` - 提交数据

#### TrojanTidalab
- `GET /server/TrojanTidalab/config` - 获取配置
- `GET /server/TrojanTidalab/user` - 获取用户
- `POST /server/TrojanTidalab/submit` - 提交数据

### 5. 用户相关 (User)
- `GET /user/resetSecurity` - 重置安全设置
- `GET /user/info` - 获取用户信息
- `POST /user/changePassword` - 修改密码
- `POST /user/update` - 更新用户信息
- `GET /user/getSubscribe` - 获取订阅信息
- `GET /user/getStat` - 获取统计信息
- `GET /user/checkLogin` - 检查登录状态
- `POST /user/transfer` - 转账
- `POST /user/getQuickLoginUrl` - 获取快速登录 URL
- `GET /user/getActiveSession` - 获取活跃会话
- `POST /user/removeActiveSession` - 移除活跃会话

#### 订单 (Order)
- `POST /user/order/save` - 保存订单
- `POST /user/order/checkout` - 结算订单
- `GET /user/order/check` - 检查订单
- `GET /user/order/detail` - 订单详情
- `GET /user/order/fetch` - 获取订单列表
- `GET /user/order/getPaymentMethod` - 获取支付方式
- `POST /user/order/cancel` - 取消订单

#### 套餐 (Plan)
- `GET /user/plan/fetch` - 获取套餐列表

#### 邀请 (Invite)
- `GET /user/invite/save` - 保存邀请
- `GET /user/invite/fetch` - 获取邀请列表
- `GET /user/invite/details` - 邀请详情

#### 公告 (Notice)
- `GET /user/notice/fetch` - 获取公告列表

#### 工单 (Ticket)
- `POST /user/ticket/reply` - 回复工单
- `POST /user/ticket/close` - 关闭工单
- `POST /user/ticket/save` - 保存工单
- `GET /user/ticket/fetch` - 获取工单列表
- `POST /user/ticket/withdraw` - 撤销工单

#### 服务器 (Server)
- `GET /user/server/fetch` - 获取服务器列表

#### 优惠券 (Coupon)
- `POST /user/coupon/check` - 检查优惠券

#### 礼品卡 (Gift Card)
- `POST /user/gift-card/check` - 检查礼品卡
- `POST /user/gift-card/redeem` - 兑换礼品卡
- `GET /user/gift-card/history` - 礼品卡历史
- `GET /user/gift-card/detail` - 礼品卡详情
- `GET /user/gift-card/types` - 礼品卡类型

#### Telegram
- `GET /user/telegram/getBotInfo` - 获取 Telegram Bot 信息

#### 通用 (Comm)
- `GET /user/comm/config` - 获取配置
- `POST /user/comm/getStripePublicKey` - 获取 Stripe 公钥

#### 知识库 (Knowledge)
- `GET /user/knowledge/fetch` - 获取知识库列表
- `GET /user/knowledge/getCategory` - 获取知识库分类

#### 统计 (Stat)
- `GET /user/stat/getTrafficLog` - 获取流量日志

## V2 版本 API

### 1. 管理员相关 (Admin)
注意：管理员路径是动态的，默认为 `/{secure_path}`，其中 `secure_path` 在配置中定义。

#### 配置管理 (Config)
- `GET /{secure_path}/config/fetch` - 获取配置
- `POST /{secure_path}/config/save` - 保存配置
- `GET /{secure_path}/config/getEmailTemplate` - 获取邮件模板
- `GET /{secure_path}/config/getThemeTemplate` - 获取主题模板
- `POST /{secure_path}/config/setTelegramWebhook` - 设置 Telegram Webhook
- `POST /{secure_path}/config/testSendMail` - 测试发送邮件

#### 套餐管理 (Plan)
- `GET /{secure_path}/plan/fetch` - 获取套餐列表
- `POST /{secure_path}/plan/save` - 保存套餐
- `POST /{secure_path}/plan/drop` - 删除套餐
- `POST /{secure_path}/plan/update` - 更新套餐
- `POST /{secure_path}/plan/sort` - 套餐排序

#### 服务器组管理 (Server Group)
- `GET /{secure_path}/server/group/fetch` - 获取服务器组列表
- `POST /{secure_path}/server/group/save` - 保存服务器组
- `POST /{secure_path}/server/group/drop` - 删除服务器组

#### 服务器路由管理 (Server Route)
- `GET /{secure_path}/server/route/fetch` - 获取服务器路由列表
- `POST /{secure_path}/server/route/save` - 保存服务器路由
- `POST /{secure_path}/server/route/drop` - 删除服务器路由

#### 服务器管理 (Server Manage)
- `GET /{secure_path}/server/manage/getNodes` - 获取节点列表
- `POST /{secure_path}/server/manage/update` - 更新服务器
- `POST /{secure_path}/server/manage/save` - 保存服务器
- `POST /{secure_path}/server/manage/drop` - 删除服务器
- `POST /{secure_path}/server/manage/copy` - 复制服务器
- `POST /{secure_path}/server/manage/sort` - 服务器排序

#### 订单管理 (Order)
- `ANY /{secure_path}/order/fetch` - 获取订单列表
- `POST /{secure_path}/order/update` - 更新订单
- `POST /{secure_path}/order/assign` - 分配订单
- `POST /{secure_path}/order/paid` - 标记订单为已支付
- `POST /{secure_path}/order/cancel` - 取消订单
- `POST /{secure_path}/order/detail` - 订单详情

#### 用户管理 (User)
- `ANY /{secure_path}/user/fetch` - 获取用户列表
- `POST /{secure_path}/user/update` - 更新用户
- `GET /{secure_path}/user/getUserInfoById` - 根据 ID 获取用户信息
- `POST /{secure_path}/user/generate` - 生成用户
- `POST /{secure_path}/user/dumpCSV` - 导出 CSV
- `POST /{secure_path}/user/sendMail` - 发送邮件
- `POST /{secure_path}/user/ban` - 封禁用户
- `POST /{secure_path}/user/resetSecret` - 重置密钥
- `POST /{secure_path}/user/setInviteUser` - 设置邀请用户
- `POST /{secure_path}/user/destroy` - 删除用户

#### 统计管理 (Stat)
- `GET /{secure_path}/stat/getOverride` - 获取覆盖统计
- `GET /{secure_path}/stat/getStats` - 获取统计信息
- `GET /{secure_path}/stat/getServerLastRank` - 获取服务器最后排名
- `GET /{secure_path}/stat/getServerYesterdayRank` - 获取服务器昨日排名
- `GET /{secure_path}/stat/getOrder` - 获取订单统计
- `ANY /{secure_path}/stat/getStatUser` - 获取用户统计
- `GET /{secure_path}/stat/getRanking` - 获取排名
- `GET /{secure_path}/stat/getStatRecord` - 获取统计记录
- `GET /{secure_path}/stat/getTrafficRank` - 获取流量排名

#### 公告管理 (Notice)
- `GET /{secure_path}/notice/fetch` - 获取公告列表
- `POST /{secure_path}/notice/save` - 保存公告
- `POST /{secure_path}/notice/update` - 更新公告
- `POST /{secure_path}/notice/drop` - 删除公告
- `POST /{secure_path}/notice/show` - 显示/隐藏公告
- `POST /{secure_path}/notice/sort` - 公告排序

#### 工单管理 (Ticket)
- `ANY /{secure_path}/ticket/fetch` - 获取工单列表
- `POST /{secure_path}/ticket/reply` - 回复工单
- `POST /{secure_path}/ticket/close` - 关闭工单

#### 优惠券管理 (Coupon)
- `ANY /{secure_path}/coupon/fetch` - 获取优惠券列表
- `POST /{secure_path}/coupon/generate` - 生成优惠券
- `POST /{secure_path}/coupon/drop` - 删除优惠券
- `POST /{secure_path}/coupon/show` - 显示/隐藏优惠券
- `POST /{secure_path}/coupon/update` - 更新优惠券

#### 礼品卡管理 (Gift Card)
##### 模板管理
- `ANY /{secure_path}/gift-card/templates` - 获取模板列表
- `POST /{secure_path}/gift-card/create-template` - 创建模板
- `POST /{secure_path}/gift-card/update-template` - 更新模板
- `POST /{secure_path}/gift-card/delete-template` - 删除模板

##### 代码管理
- `POST /{secure_path}/gift-card/generate-codes` - 生成代码
- `ANY /{secure_path}/gift-card/codes` - 获取代码列表
- `POST /{secure_path}/gift-card/toggle-code` - 切换代码状态
- `GET /{secure_path}/gift-card/export-codes` - 导出代码
- `POST /{secure_path}/gift-card/update-code` - 更新代码
- `POST /{secure_path}/gift-card/delete-code` - 删除代码

##### 使用记录
- `ANY /{secure_path}/gift-card/usages` - 获取使用记录

##### 统计信息
- `ANY /{secure_path}/gift-card/statistics` - 获取统计信息
- `GET /{secure_path}/gift-card/types` - 获取类型列表

#### 知识库管理 (Knowledge)
- `GET /{secure_path}/knowledge/fetch` - 获取知识库列表
- `GET /{secure_path}/knowledge/getCategory` - 获取分类
- `POST /{secure_path}/knowledge/save` - 保存知识库
- `POST /{secure_path}/knowledge/show` - 显示/隐藏知识库
- `POST /{secure_path}/knowledge/drop` - 删除知识库
- `POST /{secure_path}/knowledge/sort` - 知识库排序

#### 支付管理 (Payment)
- `GET /{secure_path}/payment/fetch` - 获取支付列表
- `GET /{secure_path}/payment/getPaymentMethods` - 获取支付方式
- `POST /{secure_path}/payment/getPaymentForm` - 获取支付表单
- `POST /{secure_path}/payment/save` - 保存支付
- `POST /{secure_path}/payment/drop` - 删除支付
- `POST /{secure_path}/payment/show` - 显示/隐藏支付
- `POST /{secure_path}/payment/sort` - 支付排序

#### 系统管理 (System)
- `GET /{secure_path}/system/getSystemStatus` - 获取系统状态
- `GET /{secure_path}/system/getQueueStats` - 获取队列统计
- `GET /{secure_path}/system/getQueueWorkload` - 获取队列工作负载
- `GET /{secure_path}/system/getQueueMasters` - 获取队列主控
- `GET /{secure_path}/system/getSystemLog` - 获取系统日志
- `GET /{secure_path}/system/getHorizonFailedJobs` - 获取 Horizon 失败任务
- `POST /{secure_path}/system/clearSystemLog` - 清除系统日志
- `GET /{secure_path}/system/getLogClearStats` - 获取日志清理统计

#### 主题管理 (Theme)
- `GET /{secure_path}/theme/getThemes` - 获取主题列表
- `POST /{secure_path}/theme/upload` - 上传主题
- `POST /{secure_path}/theme/delete` - 删除主题
- `POST /{secure_path}/theme/saveThemeConfig` - 保存主题配置
- `POST /{secure_path}/theme/getThemeConfig` - 获取主题配置

#### 插件管理 (Plugin)
- `GET /{secure_path}/plugin/types` - 获取插件类型
- `GET /{secure_path}/plugin/getPlugins` - 获取插件列表
- `POST /{secure_path}/plugin/upload` - 上传插件
- `POST /{secure_path}/plugin/delete` - 删除插件
- `POST /{secure_path}/plugin/install` - 安装插件
- `POST /{secure_path}/plugin/uninstall` - 卸载插件
- `POST /{secure_path}/plugin/enable` - 启用插件
- `POST /{secure_path}/plugin/disable` - 禁用插件
- `GET /{secure_path}/plugin/config` - 获取插件配置
- `POST /{secure_path}/plugin/config` - 更新插件配置
- `POST /{secure_path}/plugin/upgrade` - 升级插件

#### 流量重置管理 (Traffic Reset)
- `GET /{secure_path}/traffic-reset/logs` - 获取日志
- `GET /{secure_path}/traffic-reset/stats` - 获取统计
- `GET /{secure_path}/traffic-reset/user/{userId}/history` - 获取用户历史
- `POST /{secure_path}/traffic-reset/reset-user` - 重置用户流量

### 2. 认证相关 (Passport)
与 V1 版本相同：
- `POST /passport/auth/register` - 用户注册
- `POST /passport/auth/login` - 用户登录
- `GET /passport/auth/token2Login` - Token 登录
- `POST /passport/auth/forget` - 忘记密码
- `POST /passport/auth/getQuickLoginUrl` - 获取快速登录 URL
- `POST /passport/auth/loginWithMailLink` - 邮件链接登录
- `POST /passport/comm/sendEmailVerify` - 发送邮箱验证
- `POST /passport/comm/pv` - 页面浏览统计

### 3. 用户相关 (User)
仅包含两个端点：
- `GET /user/resetSecurity` - 重置安全设置
- `GET /user/info` - 获取用户信息

</details>
