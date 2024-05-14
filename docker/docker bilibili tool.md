B站自动执行任务的工具

点赞，投币，涨经验，签到等等功能



```
docker run -d --name="bili" \
    -v /bili/Logs:/app/Logs \
    -e Ray_DailyTaskConfig__Cron="0 15 * * *" \
    -e Ray_LiveLotteryTaskConfig__Cron="0 22 * * *" \
    -e Ray_UnfollowBatchedTaskConfig__Cron="0 6 1 * *" \
    -e Ray_VipBigPointConfig__Cron="7 1 * * *" \
    ghcr.io/raywangqvq/bilibili_tool_pro
```






扫码登录启动脚本
```
docker exec -it bili bash -c "dotnet Ray.BiliBiliTool.Console.dll --runTasks=Login"
```


查看日志
```
docker logs -f bili
```


每天的日志记录存放在`/bili/Logs`目录下

可以查看有没有出异常，或者掉登录的情况


[官方配置说明
](https://github.com/RayWangQvQ/BiliBiliTool.Docs/blob/main/configuration.md)


---
