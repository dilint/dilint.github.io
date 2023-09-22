+++
title = 'Set Up Docker'
date = 2023-09-22T21:00:30+08:00
draft = false
math = true
tags = ["docker", "install"]
+++

> 个人第一次使用经过：在有基本的容器原理前提下，`windows安装`后   根据 `Flask+Docker` 一文尝试未果（命令、墙），然后配置了`阿里镜像`，看了 `命令入门视频`,`linux安装`后部署上最基本web后端（fastapi）。
# 使用概览
## 初级
[Flask+Docker](https://zhuanlan.zhihu.com/p/78432719)   `1`
[FastApi+Docker](https://blog.csdn.net/wenxingchen/article/details/119144508?ops_request_misc=&request_id=&biz_id=102&utm_term=fastapi%20docker&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduweb~default-1-119144508.nonecase&spm=1018.2226.3001.4187)

---
# 使用过程
## 安装
[Windows](https://hub.docker.com/)
[linux](https://mirrors.tuna.tsinghua.edu.cn/help/docker-ce/)
## 配置国内镜像
[国内镜像地址](https://zhuanlan.zhihu.com/p/347643668)`2`

[阿里云配置指南](https://cr.console.aliyun.com/cn-hangzhou/instances/mirrors)

## 阿里云仓库使用
[仓库地址](https://cr.console.aliyun.com/cn-hangzhou/instance/repositories)

[pull push](https://cr.console.aliyun.com/repository/cn-hangzhou/wells0/fastapi/details)

## 命令
[入门视频](https://www.bilibili.com/video/BV11L411g7U1?p=4&vd_source=76501560d60e3e64d15a0b344c7d1028)  `3`[入门文档](https://docker.easydoc.net/doc/81170005/cCewZWoN/lTKfePfP)
[详细介绍](https://docker-practice.github.io/zh-cn/image/dockerfile/workdir.html)
[docker-compose实例](https://github.com/eugenp/tutorials)
[删除容器](https://www.php.cn/docker/444961.html#:~:text=%E5%88%A0%E9%99%A4docker%E9%87%8C%E5%BB%BA%E7%AB%8B%E7%9A%84%E5%AE%B9%E5%99%A8%E7%9A%84%E6%96%B9%E6%B3%95%EF%BC%9A%E9%A6%96%E5%85%88%E4%BD%BF%E7%94%A8docker%20-s%20-a%E5%91%BD%E4%BB%A4%E5%88%97%E5%87%BA%E6%89%80%E6%9C%89%E7%9A%84%E5%AE%B9%E5%99%A8%EF%BC%9B%E7%84%B6%E5%90%8E%E4%BD%BF%E7%94%A8docker,stop%20ID%E5%91%BD%E4%BB%A4%E5%81%9C%E6%AD%A2%E8%A6%81%E5%88%A0%E9%99%A4%E7%9A%84%E5%AE%B9%E5%99%A8%EF%BC%9B%E6%9C%80%E5%90%8E%E4%BD%BF%E7%94%A8docker%20rm%20ID%E5%91%BD%E4%BB%A4%E5%88%A0%E9%99%A4%E5%AE%B9%E5%99%A8%E5%8D%B3%E5%8F%AF%E3%80%82)

- 运行
```bash
docker run -p 8000:8000 -t 
```
- 进入终端 
```bash
$ sudo docker ps  
$ sudo docker exec -it 775c7c9ee1e1 /bin/bash  
```

# 后续使用遇到的问题
## 命令
```
docker ps 查看当前运行中的容器
docker images 查看镜像列表
docker rm container-id 删除指定 id 的容器
docker stop/start container-id 停止/启动指定 id 的容器
docker rmi image-id 删除指定 id 的镜像
docker volume ls 查看 volume 列表
docker network ls 查看网络列表
sudo docker exec -it container-id /bin/bash 进入容器
```

## Mysql
[设置密码](https://blog.csdn.net/belvine/article/details/89553972)
[Mysql 常用命令](https://blog.csdn.net/ichen820/article/details/106230691/?utm_medium=distribute.pc_relevant.none-task-blog-2~default~baidujs_baidulandingword~default-0--blog-80858073.pc_relevant_multi_platform_whitelistv3&spm=1001.2101.3001.4242.1&utm_relevant_index=3)

---
# 其他
## Ubuntu
[apt更换镜像](https://mirrors.tuna.tsinghua.edu.cn/help/ubuntu/)
[解除设置的静态ip](https://zhidao.baidu.com/question/1899783785946573180.html)
[删除docker](https://cloud.tencent.com/developer/article/1541011)
[将普通用户加入docker组](https://cloud.tencent.com/developer/article/1912506)
[用户管理命令](https://blog.csdn.net/weixin_44691296/article/details/107415492)
## virtualbox配置(失败)
[安装一](https://blog.csdn.net/Inochigohan/article/details/119791518?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522166947302016800182135386%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D&request_id=166947302016800182135386&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~top_positive~default-1-119791518-null-null.142^v66^control,201^v3^control_1,213^v2^t3_esquery_v3&utm_term=virtualbox%E5%AE%89%E8%A3%85ubuntu%E6%95%99%E7%A8%8B&spm=1018.2226.3001.4187)

---
# 服务器部署顺序
## 配置
[linux安装docker](https://mirrors.tuna.tsinghua.edu.cn/help/docker-ce/)
[阿里云配置指南](https://cr.console.aliyun.com/cn-hangzhou/instances/mirrors)
## 获取镜像
[阿里云pull push](https://cr.console.aliyun.com/repository/cn-hangzhou/wells0/fastapi/details)
或者
```bash
git clone $仓库
docker compose build 
docker compose up
```
