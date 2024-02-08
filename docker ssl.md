###  通过docker直接申请Letsencrypt的SSL证书

1、域名解析到cloudfalre

2、安装docker环境

3、本地创建cloudflare的api token

```
nano /root/cf.ini
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
