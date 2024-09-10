### SRS流媒体推流直播系统

```
docker run --restart always -d --name oryx -v $HOME/data:/data -p 8080:2022 -p 8443:2443 -p 1935:1935 -p 8000:8000/udp -p 10080:10080/udp ossrs/oryx:v5
```

后台web端口为`8080`


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
