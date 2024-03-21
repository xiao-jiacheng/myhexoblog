---
title: hexo博客站部署
author: Chaos
date: 2024/2/3
---



## docker 部署
### docker 镜像拉取   ： ```docker pull bloodstar/hexo``` 

hexo docker 一键部署环境。增加中国环境适配和常用软件。https://registry.hub.docker.com/r/bloodstar/hexo/

### 创建docker 容器
- 博客站网页默认使用4000端口  
- 首次建站创建目录存放博客源文件 如：/hexo/myhexoblog 
- docker 下工作目录为/app ，将对应hexo的源文件夹映射为/app 
- 此docker启动会自动完成建站

``` bash
docker create --name=hexo \
-e HEXO_SERVER_PORT=4000 \
-e GIT_USER="xiaojiacheng" \
-e GIT_EMAIL="xiao_jiacheng@163.com" \
-v /hexo/myhexoblog:/app \
-p 4000:4000 \
bloodstar/hexo
```


### 查看维护
访问博客站
访问  ip:4000 可访问博客站，初始有一个hello world 界面，能访问到这个界面部署成功。

![](http://nas.xiaojiacheng.top:5543//blog_pic/uploads/2024/03/20240320150246.png)


## 非docker部署
### 前置条件

- git
- node  
node 建议使用较新版本，参考node与hexo 版本支持  https://hexo.io/zh-cn/docs/



1.创建hexo blog 源文件夹，创建一个新文件夹用于存放博客源文件，如 /hexo/myhexoblog

2.安装hexo 

``` bash
cd /hexo/myhexoblog
npm install -g hexo-cli  
#这里安装hexo直在当前文件夹下，若需要全局生效，需添加到环境变量
```

3.建站

- 若使用空文件夹建站，执行命令在文件夹下创建所需要
```
$ hexo init .
$ npm install
```




