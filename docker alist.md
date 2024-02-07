###  alist网盘挂载

```
docker run -d --restart=unless-stopped -v /etc/alist:/opt/alist/data -p 54321:5244 -e PUID=0 -e PGID=0 -e UMASK=022 --name="alist" xhofe/alist:latest
```


##### 手动设置一个密码

```
docker exec -it alist ./alist admin set 你的密码
```

进入方式为：`ip:54321`
端口可自行修改
