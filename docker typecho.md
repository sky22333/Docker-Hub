## Docker部署typecho



```
docker run --name typecho-server -p 8080:80 -e TYPECHO_SITE_URL=https://your-domain.com -d joyqi/typecho:nightly-php7.4-apache
```

#### 主题文件路径`usr/themes`
