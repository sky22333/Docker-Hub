##  docker部署ddns-go域名动态解析服务

- 挂载主机目录, docker host模式。
```
docker run -d --name ddns-go --restart=always --net=host -v ./ddns:/root jeessy/ddns-go
```

> 在浏览器中打开`http://主机IP:9876`并修改你的配置。
>
> 进入面板后先设置用户名和密码，否则无法开启公网访问。


- 重置密码
```
docker exec ddns-go ./ddns-go -resetPassword 自定义密码

docker restart ddns-go
```
