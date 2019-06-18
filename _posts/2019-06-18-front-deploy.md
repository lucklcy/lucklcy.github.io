---
layout: post
title: "前端项目自动化构建"
date: 2019-06-18
author: lucklcy
cover: "/assets/img/vim.jpg"
tags: Deploy
---

![image](/assets/img/deploy/001.png)

## 写在前面

随着前端技术的发展，大量前端资源的需求出现，Web 前端体验最大的改进就是富客户端，客户端资源非常庞大，前端项目的复杂度与日俱增，功能拆分，模块化的需求迫在眉睫。另外随着 ECMAScript 版本的更迭带来很多 javascript 的新特性，但浏览器的支持滞后，开发人员又渴望使用新特性，如何保证新特性的兼容性也成为了迫切需要解决的问题。为解决这些问题，各种框架，打包编译工具如雨后春笋展露尖角。例如 vue.js + babel + webpack。但是这就导致了代码不再是直接发布到线上，而是要编译，做预处理。另外为保证资源的快速加载，cdn 加速也是必要的。

前端项目自动化构建就是为了解决这一系列的问题。

## 什么是 NPM 与 Webpack

类似 java 的 maven 项目，我们可以使用 pom 来描述项目以及项目的依赖，这样我们就可以使用社区发布的大量 jar 包来完成我们想要的功能，而不必一行一行的实现所有的功能，另外我们也可以发布我们的项目到社区，供其他人使用。在前端项目中我们使用 npm 来进行依赖模块管理,它使用 package.json 来描述项目与依赖。

本质上，webpack 是一个现代 JavaScript 应用程序的静态模块打包工具。当 webpack 处理应用程序时，它会在内部构建一个 依赖图(dependency graph)，此依赖图会映射项目所需的每个模块，并生成一个或多个 bundle。

借助 webpack 与 npm 我们就能将前端项目组合模块，按照一定的规则编译生成生产用的资源包。其中借助 babel 我们可以将 ECMAScript 2015+ 版本的代码转换为向后兼容的 JavaScript 语法，以便能够运行在当前和旧版本的浏览器或其他环境中。借助 node-sass 与 sass 我们可以使用 sass 语法，其可以使我们像编写 js 代码一样去编写 css 代码，再通过编译生成浏览器可识别的 css 等等...

## 为什么需要 NPM 私服

社区提供的 package 我们可以通过 npm 官方源【或者国内的淘宝源】来获取，那么在公司内部的**私有 package 如何共享**呢？另外公司内网一般都有**环境隔离**，这时我们如何拉取外网 package 进行构建呢？答案是 npm 私服，它可以作为跳板去获取外网 package，同时内部可以将私有的 package 发布到私服在公司内部共享。

我们采用的方案是 Nexus Repository Manager 3，它也不仅仅可以用于 npm 私服，还可以用于 maven、bower 仓库等，支持范围广泛。

### 下载

#### Nexus Repository Manager 3.12.1-01

| Operating System | Link for Download                                                        |
| ---------------- | ------------------------------------------------------------------------ |
| Unix archive     | [link](http://download.sonatype.com/nexus/3/nexus-3.12.1-01-unix.tar.gz) |
| Windows archive  | [link](http://download.sonatype.com/nexus/3/nexus-3.12.1-01-win64.zip)   |
| OSX archive      | [link](http://download.sonatype.com/nexus/3/nexus-3.12.1-01-mac.tgz)     |

### 安装与配置

#### 安装 Nexus OSS

```
$ tar -xf nexus-3.12.1-01-unix.tar.gz
$ cd nexus-3.12.1-01/bin
$ ./nexus run
```

### npm 仓库配置

#### Nexus 的仓库分为以下几种：

- proxy : **代理仓库**，对远程仓库的本地代理。这里我们代理 npm 官方源以及阿里 npm 源；地址：**http://localhost:8081/repository/cnpm-proxy/**
- hosted : **本地仓库**。作为内部 package 共享仓库；地址：**http://localhost:8081/repository/cnpm-host/**
- group：**仓库组**，作为逻辑仓库组对外；这里我们将代理仓库与本地仓库添加进来统一对外提供服务；地址：**http://localhost:8081/repository/cnpm-group/**

### npm 私服使用

```
npm config set registry http://localhost:8081/repository/cnpm-group/
// 查看一下 是否从本地服务器中下载
$ npm -loglevel info install grunt
```

此时可以验证一下 是否依赖走了代理：

![image](/assets/img/deploy/002.png)

### 设置权限

把这个选项拖到右边就可以了；

![image](/assets/img/deploy/003.png)

在 admin 之外可以重新设置一个账号，以及权限。

```
// 使用以下命令进行登陆
$ npm login -–registry=http://localhost:8081/repository/cnpm-group/
```

![image](/assets/img/deploy/004.png)

共享 package 到私服；

要发布的模块，必须保证在根目录下有 package.json 文件，否则会报错。package.json 文件内容比较随意，可以用 npm init 直接自动生成。

![image](/assets/img/deploy/005.png)

在模块根目录执行 publish 命令：**【注意地址为本地仓库地址】**

```
$ npm publish -registry http://localhost:8081/repository/cnpm-host/
```

![image](/assets/img/deploy/006.png)

然后可以在本地下载一下刚才上传到私服的包：

```
$ npm install npm_test --loglevel info
```

![image](/assets/img/deploy/007.png)

## 发布与部署

我们借助**Jenkins**实现我们自动化构建流程。具体拆解如下：

1. 拉取源码到编译机；
   ![image](/assets/img/deploy/009.png)
2. 在构建步骤，执行 shell；脚本如下：
   ![image](/assets/img/deploy/010.png)
3. 打包生成文件推送到相应的服务器；
   ![image](/assets/img/deploy/011.png)

前端打包生成的是一些静态资源，包括 js，css，html，image 等；我们将之放入到 web 服务器之中对外提供服务即可，这里我们选用 nginx。为解决 cdn 加速问题，我们将站点拆分为两个域名，一个为**服务域名**，其返回的是 html，控制其缓存策略为禁止缓存，这样在新版本发布时可以实时生效；一个为**CDN 域名**，其返回的是 js，css，image 等静态资源，其缓存时间可以设置的长一点，比如 30d；

CDN 采用的是**阿里云 CDN**，设置其回源地址为 CDN 域名即可；另外，在打包过程中我们可以开启 hash 文件名，对于有变更的文件生成新的文件名，这样我们就不用担心 CDN 的缓存问题了。

以下为服务域名的 nginx 配置，cdn 域名类似。

![image](/assets/img/deploy/008.png)

## 参考文章

[Nexus Repository Manager 3 搭建 npm 私服](https://zhuanlan.zhihu.com/p/35907412)
