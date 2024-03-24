## Docker部署typecho



```
docker run --name typecho-server -p 8080:80 -e TYPECHO_SITE_URL=https://your-domain.com -d joyqi/typecho:nightly-php7.4-apache
```

`https://your-domain.com`替换为你的网址，提前反代好。

#### 主题文件目录`usr/themes`
