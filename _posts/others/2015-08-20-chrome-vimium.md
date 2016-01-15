---
layout: post
title:  chrome插件之vimium
categories: others
---

我的感言："这是一款让自己用了就回不去的插件"。

首先，自己到chrome的应用商店搜索[vimium]。

然后，点击进行安装。

备注：如果不能打开应用商店，自己想想办法吧。

## Vimium能做什么? ##

当你安装完Vimium之后，你就可以像使用vim的命令模式一样使用快捷键操作chrome浏览器。如果说世界上的编辑器有几种一个略叼的回答是三种：vim,emacs和其他编辑器。如果vim党chrome必不可少的插件之一是什么？一个略叼的回答是：Vimium。

## Vimium怎么操作? ##

这个时候你可以使用如下的命令。注意区分大小写哦。

G: 大写的G，也就是shift+g，就可以跑到页面的最低端。

gg: 就可以跑到页面的最顶端。

k,j,h,l: 控制页面的上下左右。当然如果你嫌上线移动幅度太小的话可以使用d,u。

x: 关闭窗口。

X: 打开刚才被关闭的窗口。

f/F: 查找链接的锚点。然后输入相应的快捷字母进行跳转。如果不想跳转则esc返回。

J,K: 进行Tab的切换。

o/O: 呼出输入框，搜索历史记录。

r: 刷新页面。

H: 页面后退。

L: 页面前进。

yy,p/P: 复制当期的url。p在当前页面粘贴。P在新的窗口粘贴。

/: 启用搜索模式。

......

最后放一个绝招：? 显示所有的快捷键！

## Vimium命令大全 ##

![vimium_doc]


## 总结 ##

工具虽好，但也不要太过于迷恋。

(全文完)

[vimium]:https://chrome.google.com/webstore/detail/vimium/dbepggeogbaibhgnhhndojpepiihcmeb?hl=zh-CN

{% if site.model == 'pub' %}
[vimium_doc]:   {{ site.pub.image }}vimium_doc.png 
{% else %}
[vimium_doc]:   {{ site.dev.image }}vimium_doc.png 
{% endif %}