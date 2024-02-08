###  通过docker直接申请Letsencrypt的SSL证书

1、域名解析到cloudfalre

2、安装docker环境

3、本地创建cloudflare的api token

```
nano /root/cf.inni
```

```
# Cloudflare API credentials used by Certbot
dns_cloudflare_email = cloudflare@example.com
dns_cloudflare_api_key = 0123456789abcdef0123456789abcdef01234
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
    -d domain.com
```

上面主要是把本地的证书认证信息映射到容器对应的目录，否则会提示找不到

5、执行成功之后会提示对应的证书所在的目录


---
