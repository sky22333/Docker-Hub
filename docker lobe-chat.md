### 带语音和图文识别的chat gpt网页

```
docker run -d \
  --name lobe-chat \
  -p 3210:3210 \
  -e OPENAI_API_KEY=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX \
  -e ACCESS_CODE=XXXXXX \
  lobehub/lobe-chat
```
