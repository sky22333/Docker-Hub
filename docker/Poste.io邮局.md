## 自建Poste.io邮局系统

**需要占用的端口**
```
80 - HTTP（用于 web 界面和未加密的邮件服务）
443 - HTTPS（用于加密的 web 界面和邮件服务）
25 - SMTP（用于发送邮件）
587 - SMTP（用于加密的邮件发送）
993 - IMAP（用于加密的 IMAP 邮件接收）
995 - POP3（用于加密的 POP3 邮件接收）
110 - POP3（用于未加密的 POP3 邮件接收）
143 - IMAP（用于未加密的 IMAP 邮件接收）
```


### 1：检查25端口是否能通

```
apt update && apt install telnet -y
```
```
telnet smtp.gmail.com 25
```
- 如果显示下面的信息就代表是通的
```
Connected to smtp.gmail.com.
Escape character is '^]'.
220 smtp.gmail.com ESMTP d2e1a72fcca58-71dd9d6e713sm17347b3a.23 - gsmtp
```

### 2：域名解析（以cloudflare为例）

| 类型   | 名称                  | 内容                                         | 代理状态 | TTL       |
|--------|---------------------|--------------------------------------------|----------|-----------|
| A      | mail                | 服务器IP                           | 仅 DNS   | 1 分钟    |
| CNAME  | imap                | mail.example.com                           | 仅 DNS   | 自动      |
| CNAME  | pop                 | mail.example.com                           | 仅 DNS   | 自动      |
| CNAME  | smtp                | mail.example.com                           | 仅 DNS   | 自动      |
| MX     | example.com         | mail.example.com                           | 仅 DNS   | 自动      |
| TXT    | example.com         | v=spf1 mx ~all                            | 仅 DNS   |  自动      |
| TXT    | s20241002362._domainkey   | k=rsa; p=MIIBIjA.............xXX            | 仅 DNS   |  自动      |

> 最后一条`TXT`记录需要部署完成后进面板查看名称和内容。
>
> 替换`example.com`为你的域名

### 3：`docker-compose.yaml`启动
```
services:
  mailer:
    image: analogic/poste.io
    container_name: mailer
    restart: always
    hostname: mail.example.com  # 容器内主机名替换你的域名
    network_mode: host
    environment:
      - TZ=Asia/Shanghai  # 时区设置
      - DISABLE_CLAMAV=TRUE  # 禁用 ClamAV
      - DISABLE_RSPAMD=FALSE  # 启用 Rspamd
      - DISABLE_ROUNDCUBE=FALSE  # 启用 Roundcube
    volumes:
      - ./mailer:/data
```


###  4：进入面板

- `mail.example.com`使用这个域名进入管理面板

- 第一行默认，第二行设置管理员邮箱，例如：`admin@example.com`，第三行输入管理员密码，smtp密码也是这个

- 左侧选择`系统设置`，然后上面找到TLS证书，然后申请证书。通用名默认当前域名即可，然后申请，查看日志申请完成后，保存即可。

- 然后左侧选择`虚拟域名`，然后点击域名，找到`DKlM key`，然后点击钥匙按钮激活，然后域名解析`TXT`类型的验证。

- 然后用管理员邮箱登录，测试发邮件即可。


---
### poste.io批量创建邮箱脚本
```
#!/bin/bash

# Poste.io 容器ID或名称
CONTAINER_ID="mailer"

# 邮箱的@后缀，也就是根域名
DOMAIN="example.com"

# 所有邮箱账户的统一密码
PASSWORD="asd123456"

# 如果域名不存在，则创建域名
echo "创建域名 $DOMAIN (如果不存在)"
docker exec $CONTAINER_ID poste domain:create $DOMAIN

# 创建邮箱账户
for i in {100..200}
do
    EMAIL="$i@$DOMAIN"
    echo "正在创建邮箱: $EMAIL"
    docker exec $CONTAINER_ID poste email:create $EMAIL $PASSWORD
    
    # 检查上一个命令的退出状态
    if [ $? -eq 0 ]; then
        echo "成功创建邮箱 $EMAIL"
    else
        echo "创建邮箱 $EMAIL 失败"
    fi
    
    # 可选：添加小延迟以避免对服务器造成过大压力
    sleep 1
done

echo "邮箱创建过程完成。"
```



---

### 其他邮箱

开启SMTP服务和密码

 [微软outlook邮箱教程](https://garden.1900.live/22-knowledge/%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F/windows/%E5%BC%80%E5%90%AF%E5%BE%AE%E8%BD%AF-outlook-%E9%82%AE%E7%AE%B1-pop-imap-smtp-%E6%9C%8D%E5%8A%A1%E5%92%8C%E8%8E%B7%E5%8F%96%E6%9C%8D%E5%8A%A1%E5%AF%86%E7%A0%81-%E6%8E%88%E6%9D%83%E7%A0%81)

SMTP服务器地址：谷歌邮箱`smtp.gmail.com`， 163邮箱`smtp.163.com`， 雅虎邮箱`smtp.mail.yahoo.com`。

SMTP 端口：465 (SSL)/587 (TLS)

SMTP密码：谷歌搜索`Gmail邮箱获取SMTP密码教程`

打开Gmail邮箱——点击设置——点击查看所有设置——点击转发和 POP/IMAP——启用 IMAP——点击保存更改——点击继续

点击管理你的google账号——点击安全性——点击两步验证——开启两步验证——点开两步验证——拉到最下面——点击应用专用密码——应用选择其他——自定义一个名称——点击生成
