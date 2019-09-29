---
layout: post
title: 'Nexus OSS 3 搭建npm私服'
date: 2019-03-14
author: lucklcy
cover: '/assets/img/npm.jpg'
tags: Nexus
---

# Repository Manager 3

## 简介

Nexus 是一个强大的 Maven 仓库管理器，它极大地简化了自己内部仓库的维护和外部仓库的访问。利用 Nexus 你可以只在一个地方就能够完全控制访问 和部署在你所维护仓库中的每个 Artifact 和每个 Npm package。

- Nexus 是一套“开箱即用”的系统不需要数据库，它使用文件系统加 Lucene 来组织数据。
- Nexus 使用 ExtJS 来开发界面，利用 Restlet 来提供完整的 REST APIs，通过 m2eclipse 与 Eclipse 集成使用。
- Nexus 支持 WebDAV 与 LDAP 安全身份认证。 Nexus 分为专业收费的 Nexus Pro 与免费的 Nexus OSS 两个版本。

**我们这里来讲 Nexus OSS 以及如何使用 Nexus OSS 搭建 Npm 私服,管理 Npm package.**

## 下载

#### Nexus Repository Manager 3.12.1-01

| Operating System | Link for Download                                                          |
| ---------------- | -------------------------------------------------------------------------- |
| Unix archive     | [link](http://download.sonatype.com/nexus/3/nexus-3.12.1-01-unix.tar.gz)   |
| Windows archive  | [link](http://download.sonatype.com/nexus/3/nexus-3.12.1-01-win64.zip)     |
| OSX archive      | [link](http://download.sonatype.com/nexus/3/nexus-3.12.1-01-mac.tgz)       |
| Docker Image     | [link](https://hub.docker.com/r/sonatype/nexus3/)                          |
| Cloud Templates  | [Cloud Deployments](https://help.sonatype.com/nximaster/cloud-deployments) |

#### 官方 Docker 镜像

| 项目          | 详细                                                                        |
| ------------- | --------------------------------------------------------------------------- |
| 官方镜像      | https://hub.docker.com/r/sonatype/nexus3/                                   |
| Easypack 镜像 | https://github.com/liumiaocn/easypack/tree/master/containers/standard/nexus |

## 安装与配置

#### 安装 Nexus OSS

```
$ cd /path/to/Downloads
$ tar -xf nexus-3.12.1-01-unix.tar.gz
$ cd nexus-3.12.1-01-unix
$ sudo mv nexus-3.12.1-01 /usr/local/

# 建立仓库存放目的
$ sudo mkdir /Data
$ sudo mv sonatype-work /Data/

# 建立bin运行软链
# sudo ln -s /usr/local/nexus /usr/local/nexus-2.14.1-01
# sudo ln -s /usr/loca/nexus-3.12.1-01/bin/nexus /usr/local/bin/
```

#### 配置 Nexus OSS

```
$ cd /usr/local/nexus-2.14.1-01/conf
$ sudo vim nexus-propertities

# 修改以下内容
nexus-work=/Data/nexus-repos/sonatype-work/nexus
```

#### 测试运行

```
$ nexus start

# 停止
$ nexus stop

# 重启
$ nexus restart
```

_浏览器打开http://localhost:8081/nexus，即可看到nexus oss 的欢迎界面_

_登录用户：admin，默认密码：admin123，登录后可在个人中心修改。_

## 仓库配置

#### Nexus 的仓库分为以下几种：

- proxy : 代理仓库，对远程仓库的本地代理。
- hosted : 本地仓库。
- group：仓库组，作为逻辑仓库组对外

##### 启动之后默认地址为 localhost:8081, 默认账号密码 admin/ admin123

###### Nexus Repository Manager 中添加 npm

![image](/assets/img/nexus_oss_3/3112405-6ed2f417bae14036.png)

##### 这里有三个类型的 npm 选项：

![image](/assets/img/nexus_oss_3/3112405-76bb9056ac0b4799.png)

### 文档中对这三个类型的仓库有详细的解释：

#### Proxying npm Registries

为了减少开发人员和 CI 服务器的重复下载量并提高下载速度，您应该对托管在[官方源](https://registry.npmjs.org)上的注册表进行代理。默认情况下，npm 直接访问此注册表。 您还可以代理您需要的任何其他注册表。<br/>
要代理外部 npm 注册表，您只需创建一个新的 npm（代理），如“存储库管理”中所述。

> 最小配置步骤是：

- 定义名称
- 定义远程存储的 URL，例如https://registry.npmjs.org
- 选择存储的 Blob 存储

#### Private npm Registries

私人 npm 注册表可用于上传您自己的软件包以及第三方软件包。 您可以通过在存储库管理器中设置 npm 格式的托管存储库来创建私有的 npm 注册表。 为了这些目的，建立两个独立的托管存储库是一个很好的做法。<br/>
要创建具有 npm 格式的托管存储库，只需创建一个新的 npm（托管），如“存储库管理”中所述。

> 最小配置步骤是：

- 定义名称
- 选择存储的 Blob 存储

随着软件包从存储库部署或删除，npm 注册表信息将立即更新。

#### Grouping npm Registries

存储库组是将所有 npm 注册表存储库从存储库管理器公开给您的用户的推荐方式，而不需要任何其他客户端配置。存储库组允许您将多个代理和托管存储库的聚合内容公开为一个 URL 到 npm 和其他工具。这可以通过创建一个新的 npm（组），如“存储库管理”中所述，用于 npm 存储库。

> 最小配置步骤是：

- 定义名称
- 选择存储的 Blob 存储
- 以所需的顺序将 npm 存储库添加到成员列表中

典型的有用的示例是将代理存储库分组：代理 npm 注册表，npm 托管存储库与内部软件包和另一个 npm 托管存储库与第三方软件包。<br/>
使用存储库组的 URL 作为客户端工具中的 npm 存储库 URL，您可以使用一个 URL 访问所有三个存储库中的软件包。添加的任何新软件包以及添加到组中的任何新存储库将自动可用。

---

1. **创建 Proxying npm Registries 代理仓库**

![image](/assets/img/nexus_oss_3/3112405-37ebbc0d64c830a6.png)

1. **创建 Hosted npm Registries 代理仓库**

![image](/assets/img/nexus_oss_3/3112405-66c19792d1a1af8c.png)

1. **创建 Grouping npm Registries**
   > 仓库需要注意的是组仓库要把之前的 proxy 和 hosted 都添加上。

![image](/assets/img/nexus_oss_3/3112405-4a3b73e82ef008b4.png)

## 配置 npm registry

```
npm config set registry registry = http://localhost:8081/repository/npm-all/
```

> 安装测试

```
// 查看一下 是否从本地服务器中下载
$ npm -loglevel info install grunt
```

> 此时可以验证一下 是否依赖走了代理：

![image](/assets/img/nexus_oss_3/3112405-f8a82f22a035ae41.png)

## 设置权限

> 把这个选项拖到右边就可以了

![image](/assets/img/nexus_oss_3/3112405-d90bcf8c8fdcdb2b.png)

> 在 admin 之外可以重新设置一个账号，以及权限

![image](/assets/img/nexus_oss_3/3112405-3a59e0300667d076.png)

```
// 使用
$ npm login –registry=http://localhost:8081/repository/mygroup
// 进行登陆，需要填写账号、密码以及邮箱。
```

![image](/assets/img/nexus_oss_3/3112405-65b42a283b08a7cb.png)

> 发布到 hosted

_要发布的模块，必须保证在根目录下有 package.json 文件，否则会报错。_
_<br/>pachage.json 文件内容比较随意，如果比较懒 可以用 npm init 直接自动生成。_

```
 { "name": "myhost", "version": "1.0.0" }
```

> 然后执行发布命令：

```
$ npm publish –registry http://localhost:8081/repository/myhost
```

![image](/assets/img/nexus_oss_3/3112405-7ff83834a03ba6c5.png)

> 然后我遇到了报错，按照报错执行

```
$ npm adduser –registry http://localhost:8081/repository/myhost
```

> 这里值得注意的是， adduser 的时候要指明是哪个 rep，要不就会 add 到默认的.npmrc 里添加的 rep 中。

![image](/assets/img/nexus_oss_3/3112405-b9cd8079d4a9c1be.png)

> 添加完用户之后，再执行发布命令就 ok 了。

![image](/assets/img/nexus_oss_3/3112405-394b1c8bce17b24a.png)

> package.json 可以指定 name 和 version 并且可以添加发布路径的配置：

```
{
    "name": "intro",
    "version": "1.0.0",
    "publishConfig" : {
        "registry" : "http://localhost:8081/repository/myhost/"
    }
}
```

> 这样的话发布直接用 npm publish 就可以了。

发布之后可以在 系统中的 左侧 browse 中的 assets 中看到上传的资源

![image](/assets/img/nexus_oss_3/3112405-70f6dd148c39055d.png)

> group 中可以同时看到 proxy 和 hosted 的资源， proxy 会存下第一次下载过的目录，之后再下载时走的是代理，hosted 存的是本地上传的包资源。

然后可以在本地下载一下刚才上传到 hosted 的包：

![image](/assets/img/nexus_oss_3/3112405-23f5a0982a8abcc7.png)
