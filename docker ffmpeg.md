### docker版无人值守推流直播：

先把视频剪辑合并成一个视频，视频需MP4格式，然后放入`/home/videos`目录下


```
mkdir /home/videos && cd /home/videos
```



```
docker run -d --restart unless-stopped \
  --network host \
  -v 视频文件目录:/config \
  linuxserver/ffmpeg \
  /bin/bash -c "while true; do for file in /config/*.mp4; do ffmpeg -i \$file -c copy -f flv 推流地址/密钥; done; sleep 1; done"
```

---

##  带宽码率推荐:

| 视频清晰度    | 建议视频码率 (kbps) | 音频码率 (kbps) | 大约占用带宽 (Mbps) |
|-------------|-------------------|----------------|------------------|
| 标清 480p  | 500 - 1500        | 128            | 1 - 2     |
| 高清 720p  | 1500 - 4000       | 128            | 2 - 4      |
| 超清 1080p | 3000 - 6000       | 128            | 4 - 7      |
| 2K           | 8000 - 20000      | 128            | 9 - 20     |
| 4K           | 15000 - 50000     | 128            | 15 - 50    |



---



