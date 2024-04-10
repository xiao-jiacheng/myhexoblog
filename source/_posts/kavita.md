---
title: kavita
author: Chaos
date: 2024/4/9
---


## kavita

个人漫画管理


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
            - "5000:5000" # Change the public port (the first 5000) if you have conflicts with other services
        restart: unless-stopped
```

支持漫画格式
png 打包zip cbz
pdf