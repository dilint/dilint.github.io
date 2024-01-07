+++
title = 'Set Up Blog'
date = 2023-09-20T13:21:45+08:00
draft = false
tags = ['hugo', 'github', 'install']
+++


**系统：win11**

**参考内容：**
- [hugo + GitHub Page(win11)](https://www.jianshu.com/p/cc73559fea2c)
- [hugo + GitHub Page(mac) --codesheep](https://www.bilibili.com/video/BV1q4411i7gL/?spm_id_from=333.1007.top_right_bar_window_history.content.click&vd_source=76501560d60e3e64d15a0b344c7d1028)
- [win10结尾crlf是否替代为linux结尾lf](https://qing.shuncs.com/post/tech/hugo/)
- [添加数学公式](https://note.qidong.name/2018/03/hugo-mathjax/)

## 目录
1. [原理](#原理)
2. [日常使用](#日常使用)
3. [参数说明](#参数说明)

## 原理

### Hugo

> **Hugo** 是一个用 [Go](https://zh.wikipedia.org/wiki/Go "Go") 编写的**静态网站生成器**
> 
> **Hugo**把用户提供的数据文件、i18n 包、配置、布局模板、静态文件，以及用 [Markdown](https://zh.wikipedia.org/wiki/Markdown "Markdown") 编写的内容，处理并生成一个完整的静态网站。较出色的功能包括多语言支持、图像处理、定制输出格式、短代码等等。而 Hugo 的“嵌套分部”(Nested sections) 功能则可以隔离不同类型的内容，例如一个同时包含博客和播客的网站。
> 
> --------wikipedia

个人使用上，就是把markdown转换为一个html。把各个html安装目录组织成目录html。

### Github page
Github page会把目录下的html以静态页面的方式显示出来，在这个应用场景下我们通过hugo框架将markdown转换为html，并生成相应的目录结构，所有生成的html在`docs`目录下，在github repository的`settings -> Pages`中，把GitHub Pages Source改为`docs`目录，点击save保存即可。

## 日常使用
1. 根目录执行
```bash
hugo new "posts/hello.md"
hugo new "about/_index.md"
```
2. 本地运行
本地打开http://localhost:1313/,其中-D参数表示看到文件中&draft=true&的内容
```bash
hugo server -D
```
3. 生成发布目录
```bash
hugo -t m10c
```
4. 发布到github
```bash
git add .
git commit -m "..."
git push
```

## 参数说明

- draft : 是否是草稿，默认是true，草稿在发布时不会显示出来，可以通过加上`hugo server -D`参数来显示草稿
- math: 是否支持数学公式，默认是false
- tags: 标签，多个标签之间用逗号分隔