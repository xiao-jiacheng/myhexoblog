---
title: Hello World
############################### Article Layout ############################### > start
# 文章布局
article:
  # 文章列表页面的文章卡片布局方案
  preview:
    scheme: landscape # landscape
    # pin icon for post
    pin_icon: volantis-static/media/twemoji/assets/svg/1f4cc.svg # https://gcore.jsdelivr.net/gh/twitter/twemoji@13.0/assets/svg/1f4cc.svg
    # auto generate title if not exist
    auto_title: true # false, true
    # auto generate excerpt if not exist
    auto_excerpt: true # false, true
    # hide excerpt
    hide_excerpt: false
    # show split line or not
    line_style: solid # hidden, solid, dashed, dotted
    # show author
    author: true # true, false
    # show readmore button
    readmore: auto # auto, always
  # 文章详情页面的文章卡片本体布局方案
  body:
    # 文章顶部信息
    # 从 meta_library 中取
    top_meta: [author, category, date, counter] #启用评论数量需在此添加
    # 文章底部信息
    # 从 meta_library 中取
    bottom_meta: [updated, tags, share]
    # ----------------
    # 文章页脚组件
    footer_widget:
      # ----------------
      # 参考资料、相关资料等 (for layout: post/docs)
      references:
        title: 参考资料
        icon: fa-solid fa-quote-left
        # 在 front-matter 中:
        #   references:
        #     - title: 某篇文章
        #       url: https://
        # 即可显示此组件。
      # ----------------
      # 相关文章，需要安装插件 (for layout: post)
      # npm i hexo-related-popular-posts
      related_posts:
        enable: false
        title: 相关文章
        icon: fa-solid fa-bookmark
        max_count: 5
        # 设为空则不使用文章头图
        placeholder_img: https://gcore.jsdelivr.net/gh/MHG-LAB/cron@gh-pages/bing/bing.jpg
      # ----------------
      # 版权声明组件 (for layout: post)
      copyright:
        enable: false
        permalink: '本文永久链接是：'
        content:
          - '博客内容遵循 署名-非商业性使用-相同方式共享 4.0 国际 (CC BY-NC-SA 4.0) 协议'
          - permalink
         # 自定义版权组件：精细到文章的版权声明
        custom: false # 开启后替代上方内容的版权显示
        customData:
          default: type1  # 默认授权声明
          #############################
          # 你可以在文章的 front-matter 覆盖默认版权声明
          # 配置示例（均可选）： 
          # copyright:
          #   type: type3           # 当前文章版权声明类型
          #   author: 张三          # 本文作者
          #   ref:                  # 原文出处
          #     title:              # 原文出处 - 标题
          #     url:                # 原文出处 - 链接
          #############################
          rules:
            type1: 
              text: <a href="https://creativecommons.org/licenses/by-nc-sa/4.0/deed.zh#" target="_blank">CC BY-NC-SA 4.0</a>
              desc: 署名-非商业性使用-相同方式共享 4.0 国际。
            type2: 
              text: 禁止转载引用
              desc: 除非获得原作者的单独授权，任何第三方不得转载！
            type3: 
              text: 原作许可协议
              desc: 本文转载自他站，转载或引用本文时，请遵守原作许可协议！
            type4: 
              text: 来自互联网
              desc: 本文来自互联网，未知来源，侵权请联系删除。
            type5:
              text: 允许规范转载
              desc: 转载请保留本文转载地址，著作权归作者所有！
            type6:
              text: 允许付费转载
              desc: 您可以联系作者通过付费方式获得授权。
            # 还能自行添加更多
      # ----------------
      # 打赏组件 (for layout: post)
      donate:
        enable: false
        images:
          - volantis-static/media/org.volantis/blog/qrcode/github@volantis.png # https://gcore.jsdelivr.net/gh/volantis-x/cdn-org/blog/qrcode/github@volantis.png
          - volantis-static/media/org.volantis/blog/qrcode/github@volantis.png # https://gcore.jsdelivr.net/gh/volantis-x/cdn-org/blog/qrcode/github@volantis.png
    # meta library
    meta_library:
      # 默认文章作者（可在 _data/author.yaml 中增加其他作者，并在 front-matter 中设置）
      # https://volantis.js.org/advanced-settings/#多人协同
      author:
        avatar: volantis-static/media/org.volantis/blog/favicon/apple-touch-icon.png # https://gcore.jsdelivr.net/gh/volantis-x/cdn-org/blog/favicon/apple-touch-icon.png
        name: Chaos
        url: /
      # 文章创建日期
      date:
        icon: fa-solid fa-calendar-alt
        title: '发布于：'
        format: 'll' # 日期格式 http://momentjs.com/docs/
      # 文章更新日期
      updated:
        icon: fa-solid fa-edit
        title: '更新于：'
        format: 'll' # 日期格式 http://momentjs.com/docs/
      # 文章分类
      category:
        icon: fa-solid fa-folder-open
      # 文章浏览计数
      counter:
        icon: fa-solid fa-eye
        unit: '次浏览'
      # waline 文章评论数量
      walinecount:
        icon: fa-solid fa-comment-dots
        desc: '条评论' # 条评论
      # artalk 文章评论数量
      artalkcount:
        icon: fa-solid fa-comment-dots
        desc: '条评论' # 条评论
      # 文章字数和阅读时长
      wordcount:
        icon_wordcount: fa-solid fa-keyboard
        icon_duration: fa-solid fa-hourglass-half
      # 文章标签
      tags:
        icon: fa-solid fa-hashtag
      # 分享
      share:
        - id: qq
          img:  volantis-static/media/org.volantis/logo/128/qq.png #  https://gcore.jsdelivr.net/gh/volantis-x/cdn-org/logo/128/qq.png
        - id: qzone
          img: volantis-static/media/org.volantis/logo/128/qzone.png #  https://gcore.jsdelivr.net/gh/volantis-x/cdn-org/logo/128/qzone.png
        - id: weibo
          img: volantis-static/media/org.volantis/logo/128/weibo.png #  https://gcore.jsdelivr.net/gh/volantis-x/cdn-org/logo/128/weibo.png
        - id: # qrcode # 当id为qrcode时需要安装插件  npm i hexo-helper-qrcode
          img: # volantis-static/media/org.volantis/logo/128/wechat.png #  https://gcore.jsdelivr.net/gh/volantis-x/cdn-org/logo/128/wechat.png
        - id: # telegram
          img: # volantis-static/media/org.volantis/logo/128/telegram.png #  https://gcore.jsdelivr.net/gh/volantis-x/cdn-org/logo/128/telegram.png
############################### Article Layout ############################### > end
---



Welcome to [Hexo](https://hexo.io/)! This is your very first post. Check [documentation](https://hexo.io/docs/) for more info. If you get any problems when using Hexo, you can find the answer in [troubleshooting](https://hexo.io/docs/troubleshooting.html) or you can ask me on [GitHub](https://github.com/hexojs/hexo/issues).

## Quick Start

### Create a new post

``` bash
$ hexo new "My New Post"
```

More info: [Writing](https://hexo.io/docs/writing.html)

### Run server

``` bash
$ hexo server
```

More info: [Server](https://hexo.io/docs/server.html)

### Generate static files

``` bash
$ hexo generate
```

More info: [Generating](https://hexo.io/docs/generating.html)

### Deploy to remote sites

``` bash
$ hexo deploy
```

More info: [Deployment](https://hexo.io/docs/one-command-deployment.html)
