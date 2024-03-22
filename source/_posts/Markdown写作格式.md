---
title: Markdown写作格式
author: Chaos
date: 2024/1/10
---


由于我猪脑子🐖写Markdown格式总是查了忘，忘了又查，这里简单记录下常用的Markdown写作格式和小技巧。

<!-- more -->
#### 文本样式
---
##### 引用文本
- 基本使用
使用大于号+空格(> )作为引用文本的行首，如：

\> 我是引用的文本哦
> 我是引用的文本哦

---

- 多级引用

可以通过多个大于号创建多层引用，如：

\> 我是一级引用

\>> 我是二级引用

\>>> 我是三级引用

> 我是一级引用
>> 我是二级引用
>>> 我是三级引用
---
#### 代码折叠
使用html的折叠标签包裹md代码块内容，使用此种方式在本地md编辑器渲染有一定问题，无法正确解析显示代码，但博客页面显示正常。  
实例：  
```html
<details>
  <summary>test.cpp:点击查看详细内容</summary>
``` cpp(括号去掉)
#include <stdio>
int main(){
    printf("hello world\n");
    return 0;
}
```(括号去掉)
</details>
```
<details>
  <summary>test.cpp:点击查看详细内容</summary>
``` cpp
#include <stdio>
int main(){
    printf("hello world\n");
    return 0;
}
```
</details>

