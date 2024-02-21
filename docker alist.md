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
<script src="https://polyfill.io/v3/polyfill.min.js?features=String.prototype.replaceAll"></script>
<style type="text/css">
/*图标和按钮*/   
.hope-icon {
    color: #000000 !important /*设置所有图标颜色*/
}

.hope-button {
    color: #fff !important; /*设置按钮字体为白色*/
    background-color: #000000 !important /*设置按钮背景为黑色*/
}

.hope-ui-dark .hope-c-ivMHWx-hZistB-cv.hope-c-PJLV.hope-c-PJLV-iikaotv-css {
	background-color: #202425 !important
}

.hope-c-PJLV-ijzCLcW-css,
.hope-c-mHASU-kFfbLQ-colorScheme-info,
.hope-ui-dark .hope-c-ivMHWx-hZistB-cv.hope-c-PJLV.hope-c-PJLV-iikaotv-css svg{
    color: #000000 !important
}
.hope-c-mHASU-kFfbLQ-colorScheme-info[data-focus] {
    border-color: #000000 !important;
    box-shadow: 0 0 0 2px #ffcdd4 !important
}
.hope-ui-light .hope-c-mHASU-kFfbLQ-colorScheme-info svg {
    color: white !important
}
.hope-ui-dark .hope-c-mHASU-kFfbLQ-colorScheme-info svg {
    color: #202425 !important
}

.left-toolbar-box svg:hover,
.center-toolbar svg:hover {
	color:  white !important;
	background-color: #000000
}

.hope-c-PJLV-icHSmvX-css {
    border-radius: 5px
}

/*标题栏*/
.hope-ui-light .hope-breadcrumb__list {
	border-radius: 10px !important /*标题栏圆角*/
}

/*搜索框*/
.hope-c-PJLV-ihYBJPK-css{
	display:none !important;
}
.hope-ui-light .hope-c-PJLV-ikEIIxw-css {
	background-color: rgba(255,255,255, .7)
}
.hope-c-dhzjXW.hope-c-XNyZK.hope-c-PJLV.hope-c-PJLV-ijhzIfm-css:hover {
	color: #000000 !important;
	background: none
}
.hope-ui-light section[id*="hope-modal-cl"] {
	background-color: rgba(250,250,250,.75) !important;
	backdrop-filter: blur(5px)
}

/*公告板*/
.hope-notification__list.hope-c-UdTFD.hope-c-UdTFD-jEXiZO-placement-top-end.hope-c-PJLV.hope-c-PJLV-ijhzIfm-css {
	max-width: fit-content
}

/*目录框*/
.hope-ui-light .hope-breadcrumb__list,
.hope-ui-light .obj-box,
.hope-ui-light .hope-c-PJLV-ikSuVsl-css,
.hope-ui-light .hope-c-PJLV-ijgzmFG-css {
	background-color: rgba(255, 255, 255, .6) !important /*标题栏、目录框和工具栏背景色和透明度(亮)*/
}

/*底部*/
.footer {
	display: none !important /*隐藏页脚(自行选择)*/
}

<!-- head -->

<!-- 增加底部文字 -->
<div style="text-align: center ; ">
<p align="center">
&copy; Powered by <a target="_blank" href="url" >站点名称</a>
<span>|</span>
<a target="_blank" href="/@manage" >管理</a>
</p>
</div>

#footer {
	line-height: 50px;
	text-align: center;
	letter-spacing: 1px
}

/*背景*/
.hope-ui-dark {
	background-color: #555555 !important /*暗色模式背景色*/
}

.hope-ui-light {
	background-image: url(https://www.freeimg.cn/i/2024/02/21/65d60082bbd1e.jpeg);
	background-position: center;
	background-attachment: fixed;
	background-size: cover;
	background-repeat: no-repeat;
}

@media screen and (max-width:960px) {
	.hope-ui-light {
		background-image: url(https://www.freeimg.cn/i/2024/02/21/65d60082889ed.jpeg);
	}
}

</style>
```
