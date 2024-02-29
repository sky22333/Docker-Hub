### docker部署Nginx运行一个简单的静态网页

创建`zhuti`目录，将网页源码放入`/home/zhuti`目录下

```
docker run -d \
  --name mynginx \
  -p 8080:80 \
  -v /home/zhuti:/usr/share/nginx/html \
  nginx:latest
```


每次修改文件需重启nginx：
```
docker restart mynginx
```
