---
layout: post
title: "从仓库下载镜像到推送自己的项目到仓库"
date: 2020-08-11
author: lucklcy
cover: "/assets/img/docker/docker.jpg"
tags: [Docker]
---

> Docker 常用命令以及脚本

![img](/assets/img/docker/docker_flow.png)

### Dockerfile

<pre><code class="language-dockerfile"># 基础镜像【来源：官网 https://hub.docker.com/】
FROM nginx:latest
# 指明容器工作目录
WORKDIR /
# 在容器中执行命令
RUN apt-get update
RUN apt-get install vim -y
# 暴露容器端口
EXPOSE 8080
# 容器启动脚本
CMD /usr/sbin/nginx -g "daemon off;"
</code></pre>

### 以 Dockerfile 构建镜像并启动容器

<pre><code class="language-dockerfile"># image_name ：自定义镜像名称
# contaner_name : 自定义容器名称
# 8080:8080 ：容器暴露端口：本地映射端口
> docker build -t image_name .
> docker run --name contaner_name -p 8080:8080 -d image_name
</code></pre>

### 进入容器

<pre><code class="language-dockerfile">#exec ：在运行的容器中执行命令
#-d :分离模式: 在后台运行
#-i :即使没有附加也保持STDIN 打开
#-t :分配一个伪终端<br>
> docker exec -ti container_name /bin/bash
</code></pre>

### 将容器变成镜像

<pre><code class="language-dockerfile">#-a :提交的镜像作者；
#-c :使用Dockerfile指令来创建镜像；
#-m :提交时的说明文字；
#-p :在commit时，将容器暂停。
# docker commit -m  "提交说明文字" -a "作者" 要提交的容器名 提交后的镜像名：提交后的镜像tag名
> docker commit -m "commit message" -a "lucklcy" container_name  image_name:image_tag
</code></pre>

> 仓库截图 screenshot

![img](/assets/img/docker/docker_user_reposority.png)

### 将镜像推入仓库

- 重新定义标签

<pre><code class="language-dockerfile">#docker  tag  镜像id  要推入仓库的用户名/要推入的仓库名:新定义的tag 
> docker tag image_id docker_user/docker_usedr_reposority:new_image_tag
</code></pre>

- push 推送

<pre><code class="language-dockerfile">#docker push 要推入仓库的用户名/要推入的仓库名:镜像标签
> docker push docker_user/docker_usedr_reposority:new_image_tag
</code></pre>
