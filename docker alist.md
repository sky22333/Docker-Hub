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





### 美化

替换自定义头部代码

```
<!-- 引入字符串替换功能的JavaScript Polyfill -->
<script src="https://polyfill.io/v3/polyfill.min.js?features=String.prototype.replaceAll"></script>

<style type="text/css">
    /* 图标和按钮样式设置 */
    .hope-icon {
        color: #000000 !important; /* 设置所有图标颜色为黑色 */
    }

    .hope-button {
        color: #fff !important; /* 设置按钮字体为白色 */
        background-color: #000000 !important; /* 设置按钮背景为黑色 */
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
        color: #202425 !important; /* 设置暗色模式下的特定图标颜色为深灰色 */
    }

    /* 工具栏图标的悬停效果设置 */
    .left-toolbar-box svg:hover,
    .center-toolbar svg:hover {
        color: white !important; /* 设置工具栏图标悬停时的颜色为白色 */
        background-color: #000000; /* 设置工具栏图标悬停时的背景色为黑色 */
    }

    .hope-c-PJLV-icHSmvX-css {
        border-radius: 5px; /* 设置特定元素的边框半径为5px */
    }

    /* 标题栏样式设置 */
    .hope-ui-light .hope-breadcrumb__list {
        border-radius: 10px !important; /* 设置标题栏的圆角为10px */
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
        backdrop-filter: blur(5px); /* 设置背景模糊效果 */
    }

    /* 公告板样式设置 */
    .hope-notification__list.hope-c-UdTFD.hope-c-UdTFD-jEXiZO-placement-top-end.hope-c-PJLV.hope-c-PJLV-ijhzIfm-css {
        max-width: fit-content; /* 设置特定元素的最大宽度为内容宽度 */
    }

    /* 目录框和工具栏样式设置 */
    .hope-ui-light .hope-breadcrumb__list,
    .hope-ui-light .obj-box,
    .hope-ui-light .hope-c-PJLV-ikSuVsl-css,
    .hope-ui-light .hope-c-PJLV-ijgzmFG-css {
        background-color: rgba(255, 255, 255, .6) !important; /* 设置浅色模式下标题栏、目录框和工具栏的背景色和透明度 */
    }

    /* 隐藏页脚 */
    .footer {
        display: none !important; /* 隐藏页脚（可根据需要选择是否隐藏） */
    }

    /* 背景色设置 */
    .hope-ui-dark {
        background-color: #555555 !important; /* 设置暗色模式的背景色 */
    }

    .hope-ui-light {
        background-image: url(https://www.freeimg.cn/i/2024/02/21/65d60082bbd1e.jpeg); /* 设置浅色模式下的背景图（PC） */
        background-position: center;
        background-attachment: fixed;
        background-size: cover;
        background-repeat: no-repeat;
    }

    @media screen and (max-width: 960px) {
        .hope-ui-light {
            background-image: url(https://www.freeimg.cn/i/2024/02/21/65d60082889ed.jpeg); /* 设置浅色模式下的背景图（手机） */
        }
    }
</style>
```
