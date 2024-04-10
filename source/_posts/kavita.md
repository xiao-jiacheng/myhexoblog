---
title: kavita
author: Chaos
date: 2024/4/9
---


## kavita

个人漫画管理
支持漫画格式
png 打包zip cbz
pdf

### docker 部署
创建映射用的文件夹

#### 群晖 docker 设置 
在nas上部署docker，以群晖nas为例，在containermanager中

注册表下载docker镜像 ```jvmilazz0/kavita```

使用映像创建docker  端口及映射文件

![](http://nas.xiaojiacheng.top:5543//blog_pic/uploads/2024/04/20240410220104.png)

启动创建的docker
#### 命令行docker
也可以使用命令行创建docker的方式
```docker pull jvmilazz0/kavita```


docker-compose
```
services:
    kavita:
        image: jvmilazz0/kavita:latest    # Using the stable branch from the offical repo.
        container_name: kavita
        volumes:
            - ./manga:/manga            # Manga is just an example you can have the name you want. See the following
            - ./comics:/comics          # Use as many as you want
            - ./books:/books            #
            - ./data:/kavita/config     # Change './data if you want to have the config files in a different place.
                                        # /kavita/config must not be changed
        environment:
            - TZ=Asia/Shanghai
        ports:
            - "50001:5000" # Change the public port (the first 5000) if you have conflicts with other services
        restart: unless-stopped
```
启动docker
```docker-compose up -d```
### kivata 设置
使用docker部署完成后，通过  ip:port 进行访问，如上文例子使用映射端口为50001   
![](http://nas.xiaojiacheng.top:5543//blog_pic/uploads/2024/04/20240410214835.png)