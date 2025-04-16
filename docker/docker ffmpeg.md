### docker版ffmpeg无人值守推流直播：

先把MP4视频放入同目录下

```
services:
  ffmpeg:
    image: linuxserver/ffmpeg
    restart: always
    network_mode: host
    volumes:
      - ./:/video
    command: >
      -re -stream_loop -1 -i /video/视频文件名.mp4
      -c:v libx264 -preset veryfast -b:v 1500k
      -c:a aac -b:a 92k
      -f flv "推流地址和推流码"
```

或者使用 https://github.com/sky22333/zhibo

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



