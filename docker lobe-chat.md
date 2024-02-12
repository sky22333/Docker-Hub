### 带语音和图文识别的chat gpt网页

```
docker run -d \
  --name lobe-chat \
  -p 3210:3210 \
  -e OPENAI_API_KEY=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX \
  -e ACCESS_CODE=XXXXXX \
  lobehub/lobe-chat
```

#### [查看环境变量](https://github.com/lobehub/lobe-chat/wiki/Environment-Variable.zh-CN#%E9%80%9A%E7%94%A8%E5%8F%98%E9%87%8F)https://github.com/lobehub/lobe-chat/wiki/Environment-Variable.zh-CN#%E9%80%9A%E7%94%A8%E5%8F%98%E9%87%8F
