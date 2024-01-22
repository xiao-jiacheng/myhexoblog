---
title: Volantis 个性化配置
author: Chaos
date: 2024/1/9
---
博客站部署完毕，给页面来一点小小的美化，由于是前端苦手，对页面运行模式了解不深入，还是相当苦恼。  
此处记录相关美化配置，部分内容记录步骤繁琐请见谅（还有请原谅我的直男审美🤓）。  

### 基础配置
#### title 
网页标题设置在hexo根目录下找到```_config.yml```文件，这是基础配置文件（相关内容的配置在官方文档中有详细说明[https://hexo.io/zh-cn/docs/configuration]），其中```#site```部分就是基础配置的部分 (其他配置内容我还没去探索) 
配置实例：  
![](http://nas.xiaojiacheng.top:5543//blog_pic/uploads/2024/01/20240110113453.png)  
效果：  
![](http://nas.xiaojiacheng.top:5543//blog_pic/uploads/2024/01/20240110113725.png)

#### 主题配置内容
在使用了相关的主题后，不同主题支持不同个性化配置。这里使用的是```hexo-theme-volantis```主题。  
>主题配置内容在volantis官方文档中均有说明[https://volantis.js.org/v6/theme-settings/]

### live2d 
谁能拒绝一个萌萌哒看板娘呢，在自己的博客界面增加一个live2d人物，可以根据鼠标动作有相关反应。  
![](http://nas.xiaojiacheng.top:5543//blog_pic/uploads/2024/01/kbn.gif)  
#### live2d 配置
>添加方法：  
>1.获取 插件  
>2.在配置文件中配置  
 
 使用 npm下载live2d 插件
 ``` bash
 $ npm install --save hexo-helper-live2d
 ```
修改配置文件```/hexoroot/_config.yml```,添加如下代码：
``` yaml
live2d:
enable: true
scriptFrom: local
pluginRootPath: live2dw/
pluginJsPath: lib/
pluginModelPath: assets/
tagMode: false
log: false
model:
  use: live2d-widget-model-wanko   #模型选择
display:
  position: right          #模型位置
  width: 300               #模型大小
  height: 600
  hOffset : -10            #位置偏移量
  vOffset : -50
mobile:
  show: false              #移动端显示
react:
  opacity: 0.7             #透明度
```
配置内容可根据个人喜好修改  
移动端显示较窄，看板娘会占据相当一部分屏幕空间，建议移动端关闭看板娘功能。
#### 更改live2d模型
live2d模型支持使用模型替换  
>1.下载相关模型  
>2.在配置文件 modle字段修改为相应的模型名字

### 配置鼠标光标样式
volantis 主题自带的光标样式还是比较好看的，但是作为不特别会死星人，还是有必要给自己单独设置上光标样式。  


### 配置鼠标特效  
接下来给网页加一点鼠标点击特效，试问谁能拒绝一个随时能给你回应的鼠标指针（当然有不喜欢的当我没说🤭）  
#### 烟花爆炸特效
鼠标点击烟花爆炸💥效果不错，不想看博客还能在背景上看看烟花。 
- 效果  
![](http://nas.xiaojiacheng.top:5543//blog_pic/uploads/2024/01/1234.gif)  


设置烟花特效有两步： 
> 1.创建烟花特效的js文件  
> 2.在html中应用烟花特效

在 /hexo/theme_src/source/js/ 目录下创建js文件  fireworks.js  
我的项目中theme使用的是Volantis，使用的npm管理，所以源文件在 node_modules/hexo-theme-volantis/ 如果使用的老版本的hexo，使用主题源码在 theme/hexo-theme-volantis 下 


<details>
  <summary>firework.js:点击查看详细内容</summary>
``` js
function updateCoords(e) {
    pointerX = (e.clientX || e.touches[0].clientX) - canvasEl.getBoundingClientRect().left,
    pointerY = e.clientY || e.touches[0].clientY - canvasEl.getBoundingClientRect().top
}
function setParticuleDirection(e) {
    var t = anime.random(0, 360) * Math.PI / 180,
    a = anime.random(50, 180),
    n = [ - 1, 1][anime.random(0, 1)] * a;
    return {
        x: e.x + n * Math.cos(t),
        y: e.y + n * Math.sin(t)
    }
}
function createParticule(e, t) {
    var a = {};
    return a.x = e,
    a.y = t,
    a.color = colors[anime.random(0, colors.length - 1)],
    //这里可以修改粒子大小，我也不知道对不对
    a.radius = anime.random(16, 32),
    a.endPos = setParticuleDirection(a),
    a.draw = function() {
        ctx.beginPath(),
        ctx.arc(a.x, a.y, a.radius, 0, 2 * Math.PI, !0),
        ctx.fillStyle = a.color,
        ctx.fill()
    },
    a
}
function createCircle(e, t) {
    var a = {};
    return a.x = e,
    a.y = t,
    a.color = "#F00",
    a.radius = 0.1,
    a.alpha = 0.5,
    a.lineWidth = 6,
    a.draw = function() {
        ctx.globalAlpha = a.alpha,
        ctx.beginPath(),
        ctx.arc(a.x, a.y, a.radius, 0, 2 * Math.PI, !0),
        ctx.lineWidth = a.lineWidth,
        ctx.strokeStyle = a.color,
        ctx.stroke(),
        ctx.globalAlpha = 1
    },
    a
}
function renderParticule(e) {
    for (var t = 0; t < e.animatables.length; t++) {
        e.animatables[t].target.draw()
    }
}
function animateParticules(e, t) {
    for (var a = createCircle(e, t), n = [], i = 0; i < numberOfParticules; i++) {
        n.push(createParticule(e, t))
    }
    anime.timeline().add({
        targets: n,
        x: function(e) {
            return e.endPos.x
        },
        y: function(e) {
            return e.endPos.y
        },
        radius: 0.1,
        duration: anime.random(1200, 1800),
        easing: "easeOutExpo",
        update: renderParticule
    }).add({
        targets: a,
        radius: anime.random(80, 160),
        lineWidth: 0,
        alpha: {
            value: 0,
            easing: "linear",
            duration: anime.random(600, 800)
        },
        duration: anime.random(1200, 1800),
        easing: "easeOutExpo",
        update: renderParticule,
        offset: 0
    })
}
function debounce(e, t) {
    var a;
    return function() {
        var n = this,
        i = arguments;
        clearTimeout(a),
        a = setTimeout(function() {
            e.apply(n, i)
        },
        t)
    }
}
var canvasEl = document.querySelector(".fireworks");
if (canvasEl) {
    var ctx = canvasEl.getContext("2d"),
    numberOfParticules = 30,
    pointerX = 0,
    pointerY = 0,
    tap = "mousedown",
    colors = ["#FF1461", "#18FF92", "#5A87FF", "#FBF38C"],
    setCanvasSize = debounce(function() {
        canvasEl.width = 2 * window.innerWidth,
        canvasEl.height = 2 * window.innerHeight,
        canvasEl.style.width = window.innerWidth + "px",
        canvasEl.style.height = window.innerHeight + "px",
        canvasEl.getContext("2d").scale(2, 2)
    },
    500),
    render = anime({
        duration: 1 / 0,
        update: function() {
            ctx.clearRect(0, 0, canvasEl.width, canvasEl.height)
        }
    });
    document.addEventListener(tap,
    function(e) {
        "sidebar" !== e.target.id && "toggle-sidebar" !== e.target.id && "A" !== e.target.nodeName && "IMG" !== e.target.nodeName && (render.play(), updateCoords(e), animateParticules(pointerX, pointerY))
    },
    !1),
    setCanvasSize(),
    window.addEventListener("resize", setCanvasSize, !1)
}
"use strict";
function updateCoords(e) {
    pointerX = (e.clientX || e.touches[0].clientX) - canvasEl.getBoundingClientRect().left,
    pointerY = e.clientY || e.touches[0].clientY - canvasEl.getBoundingClientRect().top
}
function setParticuleDirection(e) {
    var t = anime.random(0, 360) * Math.PI / 180,
    a = anime.random(50, 180),
    n = [ - 1, 1][anime.random(0, 1)] * a;
    return {
        x: e.x + n * Math.cos(t),
        y: e.y + n * Math.sin(t)
    }
}
function createParticule(e, t) {
    var a = {};
    return a.x = e,
    a.y = t,
    a.color = colors[anime.random(0, colors.length - 1)],
    a.radius = anime.random(16, 32),
    a.endPos = setParticuleDirection(a),
    a.draw = function() {
        ctx.beginPath(),
        ctx.arc(a.x, a.y, a.radius, 0, 2 * Math.PI, !0),
        ctx.fillStyle = a.color,
        ctx.fill()
    },
    a
}
function createCircle(e, t) {
    var a = {};
    return a.x = e,
    a.y = t,
    a.color = "#F00",
    a.radius = 0.1,
    a.alpha = 0.5,
    a.lineWidth = 6,
    a.draw = function() {
        ctx.globalAlpha = a.alpha,
        ctx.beginPath(),
        ctx.arc(a.x, a.y, a.radius, 0, 2 * Math.PI, !0),
        ctx.lineWidth = a.lineWidth,
        ctx.strokeStyle = a.color,
        ctx.stroke(),
        ctx.globalAlpha = 1
    },
    a
}
function renderParticule(e) {
    for (var t = 0; t < e.animatables.length; t++) {
        e.animatables[t].target.draw()
    }
}
function animateParticules(e, t) {
    for (var a = createCircle(e, t), n = [], i = 0; i < numberOfParticules; i++) {
        n.push(createParticule(e, t))
    }
    anime.timeline().add({
        targets: n,
        x: function(e) {
            return e.endPos.x
        },
        y: function(e) {
            return e.endPos.y
        },
        radius: 0.1,
        duration: anime.random(1200, 1800),
        easing: "easeOutExpo",
        update: renderParticule
    }).add({
        targets: a,
        radius: anime.random(80, 160),
        lineWidth: 0,
        alpha: {
            value: 0,
            easing: "linear",
            duration: anime.random(600, 800)
        },
        duration: anime.random(1200, 1800),
        easing: "easeOutExpo",
        update: renderParticule,
        offset: 0
    })
}
function debounce(e, t) {
    var a;
    return function() {
        var n = this,
        i = arguments;
        clearTimeout(a),
        a = setTimeout(function() {
            e.apply(n, i)
        },
        t)
    }
}
var canvasEl = document.querySelector(".fireworks");
if (canvasEl) {
    var ctx = canvasEl.getContext("2d"),
    numberOfParticules = 30,
    pointerX = 0,
    pointerY = 0,
    tap = "mousedown",
    colors = ["#FF1461", "#18FF92", "#5A87FF", "#FBF38C"],
    setCanvasSize = debounce(function() {
        canvasEl.width = 2 * window.innerWidth,
        canvasEl.height = 2 * window.innerHeight,
        canvasEl.style.width = window.innerWidth + "px",
        canvasEl.style.height = window.innerHeight + "px",
        canvasEl.getContext("2d").scale(2, 2)
    },
    500),
    render = anime({
        duration: 1 / 0,
        update: function() {
            ctx.clearRect(0, 0, canvasEl.width, canvasEl.height)
        }
    });
    document.addEventListener(tap,
    function(e) {
        "sidebar" !== e.target.id && "toggle-sidebar" !== e.target.id && "A" !== e.target.nodeName && "IMG" !== e.target.nodeName && (render.play(), updateCoords(e), animateParticules(pointerX, pointerY))
    },
    !1),
    setCanvasSize(),
    window.addEventListener("resize", setCanvasSize, !1)
};
```
</details>
  


在 hexo/theme_src/layout/layout.ejs 中添加代码（代码行贴在 \<body\>标签内，来自前端小白踩得坑🤕）
```html
<canvas class="fireworks" style="position: fixed;left: 0;top: 0;z-index: 1; pointer-events: none;" ></canvas>
<script type="text/javascript" src="//cdn.bootcss.com/animejs/2.2.0/anime.min.js"></script>
<script type="text/javascript" src="/js/fireworks.js"></script>
```
![插入代码](http://nas.xiaojiacheng.top:5543//blog_pic/uploads/2024/01/20240110104513.png)

#### 点击粒子特效

用了一段时间点击烟花特效，感觉还是有点浮夸🤨，找了个新的点击特写，比较淡雅简洁一些，比较符合我朴素的性格。  
效果：  
![](http://nas.xiaojiacheng.top:5543//blog_pic/uploads/2024/01/234.gif)  
设置：  
在/hexo/theme_src/layout/layout.ejs 中添加以下代码，之前添加过的鼠标特效设置需要删除或注释  
![](http://nas.xiaojiacheng.top:5543//blog_pic/uploads/2024/01/20240119123728.png)
``` html
<script src="https://cdn.jsdelivr.net/gh/zyoushuo/Blog/hexo/js/mouse_click.js"></script>

```

### artalk
```
npm install artalk
docker 
docker run -d \
    --name artalk \
    -p 60003:23366 \
    -v /var/services/homes/xiaojiacheng/workspace/artalk:/data \
    --restart=always \
    artalk/artalk-go
    
create management account
docker exec -it artalk artalk admin
```