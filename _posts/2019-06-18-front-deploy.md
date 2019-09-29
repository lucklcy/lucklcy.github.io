---
layout: post
title: '前端项目自动化构建'
date: 2019-06-18
author: lucklcy
cover: '/assets/img/deploy.jpg'
tags: Deploy
---

![image](/assets/img/deploy/001.png)

## 写在前面

随着前端技术的发展，大量前端资源的需求出现，Web 前端体验最大的改进就是富客户端，客户端资源非常庞大，前端项目的复杂度与日俱增，功能拆分，模块化的需求迫在眉睫。另外随着 ECMAScript 版本的更迭带来很多 javascript 的新特性，但浏览器的支持滞后，开发人员又渴望使用新特性，如何保证新特性的兼容性也成为了迫切需要解决的问题。为解决这些问题，各种框架，打包编译工具如雨后春笋展露尖角。但是这就导致了代码不再是直接发布到线上，而是要编译，做预处理。另外为保证资源的快速加载，cdn 加速也是必要的。

## 背景

### 什么是 NPM

类似 java 的 maven 项目，我们可以使用 pom 来描述项目以及项目的依赖，这样我们就可以使用社区发布的大量 jar 包来完成我们想要的功能，而不必一行一行的实现所有的功能，另外我们也可以发布我们的项目到社区供其他人使用。在前端项目中我们使用 npm 来进行依赖模块管理,它使用 package.json 来描述项目与依赖。

### 什么是 Webpack

本质上，webpack 是一个现代 JavaScript 应用程序的静态模块打包工具。当 webpack 处理应用程序时，它会在内部构建一个 依赖图(dependency graph)，此依赖图会映射项目所需的每个模块，并生成一个或多个 bundle。

借助 webpack 与 npm 我们就能将前端项目组合模块，按照一定的规则编译生成生产用的资源包。其中借助 babel 我们可以将 ECMAScript 2015+ 版本的代码转换为向后兼容的 JavaScript 语法，以便能够运行在当前和旧版本的浏览器或其他环境中。借助 node-sass 与 sass 我们可以使用 sass 语法，其可以使我们像编写 js 代码一样去编写 css 代码，再通过编译生成浏览器可识别的 css 等等...

## 遇到的问题

### **旧的解决方案**

代码直接在开发人员本地编译好之后提交到仓库，再通过 Jenkins 将代码拉取推送到目标服务器。这样做好处就是方便，但是带来了无穷无尽的麻烦：

首先，发布需要开发人员先在本地编译构建，然后提交到仓库；并且各个开发人员的本地环境不一定完全一致，这就导致编译生成的包具有不一致性，相应的其可靠性也大大下降；再者需要将编译后的代码纳入到版本管理，这本身就是不合理的；

其次，为了 CDN 加速，编译后的代码需要推送到两个地方，一个是服务站点，另一个是 CDN 站点；一旦线上有问题，需要回滚的话，这两个站点并不能做到同步回滚，另外由于历史原因，CDN 站点的代码并没有版本控制，因此回滚一路不通；唯一的办法就是再次发版，这在紧急情况下，保证不出问题恐怕很难；

最后，这一套方案，需要大量人工干预并且各个环节存在强依赖，效率低下；

### **新方案的思考**

1. 编译不应该在研发人员本地进行，应该后移至服务器；
2. 遇到紧急问题可快速回滚；
3. 不需要过多的人为干预，开发人员提交源码后，剩下应该自动去完成；

### 难点

- **线上环境出于安全考虑，没有外网权限并且存在环境隔离，那么编译所需依赖包如何拉取？**
- **要实现快速回滚，需要保证站点与 CDN 的资源为同一份，如何改造？**

## 为什么需要 NPM 私服？

针对第一点难题，我们可以通过搭建**公司内部的 NPM 私服**去解决；

其一，线上编译机器可以通过它来获取外网的相关依赖包，它还可以缓存已经下载的依赖包，在第二次请求依赖时直接返回，加快拉取包的速度；

其二，在公司内部的私有依赖包，可以上传其上，顺便解决了私有包的共享问题；

我们采用的方案是 Nexus Repository Manager 3，它也不仅仅可以用于 npm 私服，还可以用于 maven、bower 仓库等，支持范围广泛。

### Nexus 安装与使用

#### Nexus Repository Manager 3.12.1-01 下载

| Operating System | Link for Download                                                        |
| ---------------- | ------------------------------------------------------------------------ |
| Unix archive     | [link](http://download.sonatype.com/nexus/3/nexus-3.12.1-01-unix.tar.gz) |
| Windows archive  | [link](http://download.sonatype.com/nexus/3/nexus-3.12.1-01-win64.zip)   |
| OSX archive      | [link](http://download.sonatype.com/nexus/3/nexus-3.12.1-01-mac.tgz)     |

#### 安装

```
$ tar -xf nexus-3.12.1-01-unix.tar.gz
$ cd nexus-3.12.1-01/bin
$ ./nexus run
```

#### NPM 仓库配置

- proxy : **代理仓库**，对远程仓库的本地代理。这里我们代理 npm 官方源以及阿里 npm 源；地址：**http://localhost:8081/repository/cnpm-proxy/**
- hosted : **本地仓库**。作为内部 package 共享仓库；地址：**http://localhost:8081/repository/cnpm-host/**
- group：**仓库组**，作为逻辑仓库组对外；这里我们将代理仓库与本地仓库添加进来统一对外提供服务；地址：**http://localhost:8081/repository/cnpm-group/**

包下载优先级：私有仓库==>>阿里源==>>官方源

#### NPM 私服使用

```
npm config set registry http://localhost:8081/repository/cnpm-group/
// 查看一下 是否从本地服务器中下载
$ npm -loglevel info install grunt
```

此时可以验证一下 是否依赖走了代理：

![image](/assets/img/deploy/002.png)

#### 配置账号权限

把这个选项拖到右边就可以了；

![image](/assets/img/deploy/003.png)

在 admin 之外可以重新设置一个账号，以及权限。

```
// 使用以下命令进行登陆
$ npm login -–registry=http://localhost:8081/repository/cnpm-group/
```

![image](/assets/img/deploy/004.png)

#### 共享 package 到私服；

要发布的模块，必须保证在根目录下有 package.json 文件，否则会报错。package.json 文件内容比较随意，可以用 npm init 直接自动生成。

![image](/assets/img/deploy/005.png)

在模块根目录执行 publish 命令：**【注意地址为本地仓库地址】**

```
$ npm publish -registry http://localhost:8081/repository/cnpm-host/
```

![image](/assets/img/deploy/006.png)

#### 验证上传

```
$ npm install npm_test --loglevel info
```

![image](/assets/img/deploy/007.png)

## CDN 回源问题

CDN 工作方法是客户端浏览器先检查是否有本地缓存是否过期，如果过期，则向 CDN 边缘节点发起请求，CDN 边缘节点会检测用户请求数据的缓存是否过期，如果没有过期，则直接响应用户请求，此时一个完成 http 请求结束；如果数据已经过期，那么 CDN 还需要向源站发出回源请求（back to the source request）,来拉取最新的数据。CDN 的典型拓扑图如下：

![image](/assets/img/deploy/012.png)

常规的 CDN 都有回源的，包括 ip 回源与域名回源，一般采用域名回源，其可以解决源站 ip 经常变动的问题。那么我们将回源域名解析到我们的站点域名【例如：**站点域名**：xxx.ppd.com,**CDN 域名**：xxx.ppdcdn.com，我们将**CDN**的回源地址配置为站点域名】这不就保证资源是同一份了么！直接回滚版本即可实现回滚操作，即可解决上述第二个难点；

## 发布与部署

我们借助**Jenkins**实现我们自动化构建流程。具体拆解如下：

1. 拉取源码到 Node 编译机；
   ![image](/assets/img/deploy/009.png)
2. 在构建步骤，执行 shell；【通过 NPM 私服拉取依赖】
   ![image](/assets/img/deploy/010.png)
3. 打包生成文件推送到相应的站点服务器；
   ![image](/assets/img/deploy/011.png)

前端打包生成的是一些静态资源，包括 js，css，html，image 等；我们将之放入到 web 服务器之中对外提供服务即可，这里我们选用 nginx。CDN 回源域名配置到相应的站点域名，由 nginx 提供回源服务即可。

以下为站点域名的 nginx 配置【节选】。

![image](/assets/img/deploy/008.png)
