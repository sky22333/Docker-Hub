## 自建Poste.io邮局系统

### 1：检查25端口是否能通
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

### 3：`docker-compose.yaml`启动
```
services:
  mailer:
    image: analogic/poste.io
    container_name: mailer
    restart: always
    hostname: mail.example.com  # 容器内主机名
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
