### SRS流媒体推流直播系统

```
docker run --restart always -d --name oryx -v $HOME/data:/data -p 80:2022 -p 443:2443 -p 1935:1935 -p 8000:8000/udp -p 10080:10080/udp ossrs/oryx:latest
```

后台web端口为`80`

### SRS服务端


docker安装
```
docker run -d --restart always --name srs -p 1935:1935 -p 1985:1985 -p 8080:8080 ossrs/srs:latest
```
`IP:8080`端口可进入SRS后台获取视频URL

需给8080端口反代域名并开启HTTPS

建议SRS和网站部署再同一台服务器上，播放更流畅

推流地址为：`rtmp://域名/live/livestream`

##### [SRS官方文档](https://ossrs.net/lts/zh-cn/docs/v5/doc/flv)

### 将播放器嵌入网站（以WordPress为例）

### SRS播放器

WP插件商城搜索`SRS Player`

文章插入简码：
```
[srs_player url="视频链接"]
```

视频链接必须使用HTTPS。

---
