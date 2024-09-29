# certbot申请域名证书
#### 安装certbot
```
apt update && apt install certbot -yq
```
#### 申请证书
临时占用80端口
```
sudo certbot certonly --standalone --non-interactive --agree-tos -d example.com
```
替换`example.com`示例域名，替换`youremail@example.com`示例邮箱

#### 查看自动续期任务
```
sudo certbot renew --dry-run
```
默认情况下，Certbot会将证书存储在`/etc/letsencrypt/live/example.com/`目录下

---
---

#### 使用Cloudflare的DNS方式申请证书
安装DNS插件
```
sudo apt install python3-certbot-dns-cloudflare -yq
```
创建配置文件`cloudflare.ini`，配置文件填入`api_token`
```
dns_cloudflare_api_token = YOUR_API_TOKEN
```

### 申请
```
sudo certbot certonly --dns-cloudflare --dns-cloudflare-credentials ~/cloudflare.ini --non-interactive --agree-tos -d example.com
```

### 阿里云DNS
安装插件
```
sudo apt install python3-certbot-dns-aliyun -yq
```
配置
```
dns_aliyun_access_key = YOUR_ACCESS_KEY_ID
dns_aliyun_secret_key = YOUR_ACCESS_KEY_SECRET
```


---
---

# docker一键申请

```
services:
  https-portal:
    image: steveltn/https-portal:1
    ports:
      - '80:80'
      - '443:443'
    environment:
      DOMAINS: 'example.com' # 你的域名
      STAGE: 'production' # 生产环境
      FORCE_RENEW: 'true' # 证书续订
    volumes:
      - https-portal-data:/var/lib/https-portal
      - /var:/var/lib/https-portal # 映射文件到宿主机

volumes:
  https-portal-data:
```


### dns方式申请

```
services:
  https-portal:
    image: steveltn/https-portal:1
    ports:
      - '80:80'
      - '443:443'
    environment:
      DOMAINS: 'example.com' # 你的域名
      STAGE: 'production'
      FORCE_RENEW: 'true' # 证书续订
      DNS_PROVIDER: 'cloudflare' # 或 'alidns'
      CLOUDFLARE_EMAIL: 'your-email@example.com' # 对应于Cloudflare
      CLOUDFLARE_API_KEY: 'your-cloudflare-api-token' # 对应于Cloudflare
      # ALI_KEY: 'your-ali-access-key-id' # 对应于阿里云
      # ALI_SECRET: 'your-ali-access-key-secret' # 对应于阿里云
    volumes:
      - https-portal-data:/var/lib/https-portal
      - /var:/var/lib/https-portal # 映射文件到宿主机

volumes:
  https-portal-data:
```
