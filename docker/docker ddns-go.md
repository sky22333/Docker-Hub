##  docker部署ddns-go域名动态解析服务

挂载主机目录, docker host模式。
```
docker run -d --name ddns-go --restart=always --net=host -v /opt:/root jeessy/ddns-go
```
在浏览器中打开`http://主机IP:9876`并修改你的配置

[可选] 支持启动带参数 -l监听地址 -f间隔时间(秒)
```
docker run -d --name ddns-go --restart=always --net=host -v /opt:/root jeessy/ddns-go -l
```
