###  alist网盘挂载

```
docker run -d --restart=unless-stopped -v /etc/alist:/opt/alist/data -p 54321:5244 -e PUID=0 -e PGID=0 -e UMASK=022 --name="alist" xhofe/alist:latest
```


##### 设置密码

```
docker exec -it alist ./alist admin set 你的密码
```

进入方式为：`ip:54321`

用户名为：`admin`

端口可自行修改

管理路径为`/@manage`



### 美化

替换自定义头部代码

```
<!-- 引入字符串替换功能的 JavaScript Polyfill -->
<script src="https://polyfill.alicdn.com/v3/polyfill.min.js?features=String.prototype.replaceAll"></script>

<style type="text/css">
    /* 图标和按钮样式设置 */
    .hope-icon {
        color: #393939 !important; /* 设置所有图标颜色为黑色 */
    }

    .hope-button {
        color: #fff !important; /* 设置按钮字体为白色 */
        background-color: #393939 !important; /* 设置按钮背景为黑色 */
    }

    /* 特定元素的背景颜色设置 */
    .hope-ui-dark .hope-c-ivMHWx-hZistB-cv.hope-c-PJLV.hope-c-PJLV-iikaotv-css {
        background-color: #202425 !important; /* 设置特定元素在暗色模式下的背景色 */
    }

    /* 一些文本和图标颜色设置 */
    .hope-c-PJLV-ijzCLcW-css,
    .hope-c-mHASU-kFfbLQ-colorScheme-info,
    .hope-ui-dark .hope-c-ivMHWx-hZistB-cv.hope-c-PJLV.hope-c-PJLV-iikaotv-css svg {
        color: #000000 !important; /* 设置文本和图标颜色为黑色 */
    }

    .hope-c-mHASU-kFfbLQ-colorScheme-info[data-focus] {
        border-color: #000000 !important; /* 设置具有焦点的元素边框颜色为黑色 */
        box-shadow: 0 0 0 2px #ffcdd4 !important; /* 设置具有焦点的元素阴影效果 */
    }

    .hope-ui-light .hope-c-mHASU-kFfbLQ-colorScheme-info svg {
        color: white !important; /* 设置浅色模式下的特定图标颜色为白色 */
    }

    .hope-ui-dark .hope-c-mHASU-kFfbLQ-colorScheme-info svg {
        color: #000000 !important; /* 设置暗色模式下的特定图标颜色为黑色 */
    }

    /*导航条*/ 
    /*白天模式*/
    .hope-ui-light .hope-c-PJLV-idaeksS-css {
        background-color: rgba(255, 255, 255, 0.5) !important;
        border-radius: var(--hope-radii-xl) !important;
    }

    /* 吸附到页面顶部 */
    /*顶部*/
    .hope-c-PJLV-icWrYmg-css {
        background: rgba(255, 255, 255, 0) !important;
    }

    /*导航条*/
    .hope-c-PJLV-icKsjdm-css::after {
        background: rgba(255, 255, 255, 0) !important;
    }

    /*白天模式*/
    .hope-ui-light .hope-c-PJLV-icKsjdm-css {
        background-color: rgba(255, 255, 255, 0.5) !important;
        border-radius: var(--hope-radii-xl) !important;
    }

    /* 工具栏图标的悬停效果设置 */
    .left-toolbar-box svg:hover,
    .center-toolbar svg:hover {
        color: white !important;
        background-color: #393939;
    }

    .hope-c-PJLV-icHSmvX-css {
        border-radius: 5px;
    }

    /* 标题栏样式设置 */
    .hope-ui-light .hope-breadcrumb__list {
        border-radius: 10px !important;
    }

    /* 搜索框样式设置 */
    .hope-c-PJLV-ihYBJPK-css {
        display: none !important; /* 隐藏搜索框 */
    }

    .hope-ui-light .hope-c-PJLV-ikEIIxw-css {
        background-color: rgba(255, 255, 255, .7); /* 设置浅色模式下搜索框的背景色和透明度 */
    }

    .hope-c-dhzjXW.hope-c-XNyZK.hope-c-PJLV.hope-c-PJLV-ijhzIfm-css:hover {
        color: #000000 !important; /* 设置特定元素悬停时的颜色为黑色 */
        background: none; /* 设置特定元素悬停时的背景为透明 */
    }

    /* 弹出框样式设置 */
    .hope-ui-light section[id*="hope-modal-cl"] {
        background-color: rgba(250, 250, 250, .75) !important; /* 设置浅色模式下弹出框的背景色和透明度 */
        backdrop-filter: blur(5px);
    }

    /* 公告板样式设置 */
    .hope-notification__list.hope-c-UdTFD.hope-c-UdTFD-jEXiZO-placement-top-end.hope-c-PJLV.hope-c-PJLV-ijhzIfm-css {
        max-width: fit-content;
    }

    /* 目录框和工具栏样式设置 */
    .hope-ui-light .hope-breadcrumb__list,
    .hope-ui-light .obj-box,
    .hope-ui-light .hope-c-PJLV-ikSuVsl-css,
    .hope-ui-light .hope-c-PJLV-ijgzmFG-css {
        background-color: rgba(255, 255, 255, .6) !important;
    }

    .footer span, .footer a:nth-of-type(1) {
        display: none;
    }

    .hope-ui-light {
        background-image: url(https://api.aixiaowai.cn/mcapi/mcapi.php); /* 设置浅色模式下的背景图（PC） */
        background-position: center;
        background-attachment: fixed;
        background-size: cover;
        background-repeat: no-repeat;
    }

    @media screen and (max-width: 960px) {
        .hope-ui-light {
            background-image: url(https://api.aixiaowai.cn/mcapi/mcapi.php); /* 设置浅色模式下的背景图（手机） */
        }
    }
</style>
```

### 看板娘

放入自定义内容部分

```
<script src="https://eqcn.ajz.miesnfu.com/wp-content/plugins/wp-3d-pony/live2dw/lib/L2Dwidget.min.js"></script>
 
<!-- 看板娘 -->
<link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/font-awesome/css/font-awesome.min.css">
<script src="https://cdn.jsdelivr.net/gh/stevenjoezhang/live2d-widget@latest/autoload.js"></script>
```

### 滚动字幕（放入元信息）

黑色字体

```
<marquee>
<b id="nr">(｡･∀･)ﾉﾞ嗨，欢迎来到我的小破站。帅气漂亮的小哥哥小姐姐</b> 
</marquee></div>
```

炫彩字体

```
<style>#nr{font-size:20px;margin: 0;background: -webkit-linear-gradient(left,#ffffff,#ff0000 6.25%,#ff7d00 12.5%,#ffff00 18.75%,#00ff00 25%,#00ffff 31.25%,#0000ff 37.5%,#ff00ff 43.75%,#ffff00 50%,#ff0000 56.25%,#ff7d00 62.5%,#ffff00 68.75%,#00ff00 75%,#00ffff 81.25%,#0000ff 87.5%,#ff00ff 93.75%,#ffff00 100%);-webkit-background-clip: text;-webkit-text-fill-color: transparent;background-size: 200% 100%;animation: masked-animation 2s infinite linear;}@keyframes masked-animation {0% {background-position: 0 0;}100% {background-position: -100%, 0;}}</style><div style="background-color:#333;border-radius:25px;box-shadow:0px 0px 5px #f200ff;padding:5px;margin-top:10px;margin-bottom:0px;"><marquee>
<b id="nr">(｡･∀･)ﾉﾞ嗨，欢迎来到我的小破站。帅气漂亮的小哥哥小姐姐</b> </marquee></div>
```
