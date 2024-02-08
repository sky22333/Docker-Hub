##  通过docker直接申请Letsencrypt的SSL证书


### Cloudfalre

1、域名解析到cloudfalre

2、安装docker环境

3、本地创建cloudflare的api token

```
touch /root/cf.ini
```

```
# Cloudflare API credentials used by Certbot
dns_cloudflare_email = 你的Cloudflare邮件
dns_cloudflare_api_key = 你的Cloudflare API密钥
```

4、执行如下命令直接获取到证书

```
docker run -it --rm --name certbot \
    -v "/etc/letsencrypt:/etc/letsencrypt" \
    -v "/var/lib/letsencrypt:/var/lib/letsencrypt" \
    -v "/root/cf.ini:/root/cf.ini" \
    certbot/dns-cloudflare certonly \
    --dns-cloudflare \
    --dns-cloudflare-credentials /root/cf.ini \
    -d 你的域名
```

上面主要是把本地的证书认证信息映射到容器对应的目录，否则会提示找不到

5、执行成功之后会提示对应的证书所在的目录


---



#### 阿里云

1：域名解析到阿里云

2：创建阿里云 `DNS API Token`

   在阿里云控制台中，转到 `访问控制` > `AccessKey 管理`
   
   创建一个新的 `AccessKey`，并确保为该 `AccessKey` 启用阿里云 `DNS API 权限`

3：创建 cf.ini 文件

```
touch /root/cf.ini
```

```
# Aliyun DNS credentials used by Certbot
dns_aliyun_email = <Your_Aliyun_Email>
dns_aliyun_access_key_id = <Your_Aliyun_AccessKey_ID>
dns_aliyun_access_key_secret = <Your_Aliyun_AccessKey_Secret>
```

4：执行命令获取证书

```
docker run -it --rm --name certbot \
    -v "/etc/letsencrypt:/etc/letsencrypt" \
    -v "/var/lib/letsencrypt:/var/lib/letsencrypt" \
    -v "/root/cf.ini:/root/cf.ini" \
    certbot/dns-aliyun certonly \
    --dns-aliyun \
    --dns-aliyun-credentials /root/cf.ini \
    -d 你的域名
```

5：执行成功之后会提示对应的证书所在的目录



---
