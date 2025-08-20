### docker-compose.yml 部署邮件服务
```
services:
  stalwart:
    image: stalwartlabs/stalwart:latest
    container_name: stalwart
    restart: always
    ports:
      - "443:443"
      - "8080:8080"
      - "25:25"
      - "587:587"
      - "465:465"
      - "143:143"
      - "993:993"
      - "4190:4190"
      - "110:110"
      - "995:995"
    volumes:
      - ./mail:/opt/stalwart
```

### 1：打开`http://IP:8080`

![image](/png/stalwart/2.png)
### 2：点击Settings——Network ，然后修改Hostname ，然后点击Save & Reload

![image](/png/stalwart/3.png)
![image](/png/stalwart/4.png)

### 3：点击Management——Directory——Domains，创建个域名

![image](/png/stalwart/5.png)
![image](/png/stalwart/6.png)
![image](/png/stalwart/7.png)

### 4：点击Management——Directory——Accounts，创建用户并设置用户密码

![image](/png/stalwart/8.png)
![image](/png/stalwart/9.png)
![image](/png/stalwart/10.png)
![image](/png/stalwart/11.png)

### 5：点击Management——Directory——Domains，查看需要配置的DNS记录

按照记录，一条一条添加到域名中做解析
这个时候我们还需要再添加两条
A记录，将baidu.com解析到服务器IP上
A记录，将mail.baidu.com解析到服务器IP上
接下来再来添加系统要求配置的DNS记录

![image](/png/stalwart/12.png)
![image](/png/stalwart/13.png)

### 6：点击TLS——ACME Providers，创建个自动申请证书的ACME任务，设置完后点击Save & Reload

![image](/png/stalwart/14.png)
![image](/png/stalwart/15.png)
![image](/png/stalwart/16.png)

如果你在国内服务器部署的话，ACME可能会不成功，你可以手动申请SSl证书再复制到系统里，有了证书后，这时你打开https://mail.baidu.com:4443就能正常打开了

### 7：修改服务监听
点击Server——Listeners，修改IMAP4、POP3、SMTP、的TLS选项都打开
![image](/png/stalwart/17.png)
![image](/png/stalwart/18.png)
![image](/png/stalwart/19.png)
![image](/png/stalwart/20.png)
![image](/png/stalwart/21.png)

### 8：登陆邮件客户端
下载个163的客户端，然后填入我们邮件服务器的参数
![image](/png/stalwart/22.png)
![image](/png/stalwart/23.png)

### 然后就可以正常发送邮件了
![image](/png/stalwart/24.png)

### DNS配置后续
虽然你的DNS在前面配置好了，但是有条记录自己是配不了的，需要找服务器提供商配置，那就是rDNS，也就是所谓的PTR记录，反向解析；前面我们做的都是正向解析，正向解析是指通过域名可以解析出IP，反向解析则是通过IP解析出域名。
为什么要做反向解析？PTR 反向解析主要应用在邮件服务器中，因为多数垃圾邮件发送方使用动态分配或者没有注册域名的 IP 发送垃圾邮件，以逃避追踪，所以可以在邮件服务器中拒绝接收来自无法反向解析到域名的 IP 地址发送的信息，作为一种拒收垃圾邮件的手段，启用反向解析，可以拒绝接收所有没有注册域名发来的信息，从而提升服务器IP 的信誉度。
所以你需要找到你的服务器提供商来帮你做，但是一般大厂商都能自行操作就是，像腾讯、阿里这种大厂是可以在管理台上自行操作的，境外的许多服务器提供商也支持自行操作，而运营商的服务器则不支持，需要提工单审核，审核通过后才能给配置。
### 总结

部署邮件服务器其实挺简单的，就是起个Docker容器，然后进入后台配置个主机名、域、账号、SSL证书和开启服务监听的TLS选项，配置个DNS就能用了。部署邮件服务器其实挺简单的，就是起个Docker容器，然后进入后台配置个主机名、域、账号、SSL证书和开启服务监听的TLS选项，配置个DNS就能用了。


```
文章作者：StephenJose_Dai

原始链接：https://daishenghui.club/2025/06/12/categories/Linux/Linux部署企业级邮件服务器

文章转载许可协议： "署名-非商用-相同方式共享 3.0" 转载请保留原文链接及作者。
```

