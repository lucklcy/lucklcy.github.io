---
layout: post
title: "package解析"
date: 2021-09-23
author: lucklcy
cover: "/assets/img/npm.jpg"
tags: npm
---

## 你真的了解 package.json 吗？来看看吧，这可能是最全的 package 解析

## 1. 概述

---

从我们接触前端开始，每个项目的根目录下一般都会有一个 package.json 文件，这个文件定义了当前项目所需要的各种模块，以及项目的配置信息（比如名称、版本、许可证等）。

当运行 npm install 命令的时候，会根据 package.json 文件中的配置自动下载所需的模块，也就是配置项目所需的运行和开发环境。

比如下面这个文件，只存在简单的项目名称和版本号。

```
{
  "name" : "yindong",
  "version" : "1.0.0",
}
```

package.json 文件是一个 JSON 对象，这从他的后缀名.json 就可以看出来，该对象的每一个成员就是当前项目的一项设置。比如 name 就是项目名称，version 是版本号。

当然很多人其实并不关心 package.json 的配置，他们应用的更多的是 dependencies 或 devDependencies 配置。

下面是一个更完整的 package.json 文件，详细解释一下每个字段的真实含义。

```
{
    "name": "yindong",
    "version":"0.0.1",
    "description": "antd-theme",
    "keywords":["node.js","antd", "theme"],
    "homepage": "https://zhiqianduan.com",
    "bugs":{"url":"http://path/to/bug","email":"yindong@xxxx.com"},
    "license": "ISC",
    "author": "yindong",
    "contributors":[{"name":"yindong","email":"yindong@xxxx.com"}],
    "files": "",
    "main": "./dist/default.js",
    "bin": "",
    "man": "",
    "directories": "",
    "repository": {
		"type": "git",
		"url": "https://path/to/url"
	},
    "scripts": {
      "start": "webpack serve --config webpack.config.dev.js --progress"
    },
    "config": { "port" : "8080" },
    "dependencies": {},
    "devDependencies": {
        "@babel/core": "^7.14.3",
        "@babel/preset-env": "^7.14.4",
        "@babel/preset-react": "^7.13.13",
        "babel-loader": "^8.2.2",
        "babel-plugin-import": "^1.13.3",
        "glob": "^7.1.7",
        "less": "^3.9.0",
        "less-loader": "^9.0.0",
        "style-loader": "^2.0.0",
        "webpack": "^5.38.1",
        "webpack-cli": "^4.7.0",
        "webpack-dev-server": "^3.11.2"
    },
    "peerDependencies": {
        "tea": "2.x"
    },
    "bundledDependencies": [
        "renderized", "super-streams"
    ],
    "engines": {"node": "0.10.x"},
	  "os" : [ "win32", "darwin", "linux" ],
    "cpu" : [ "x64", "ia32" ],
    "private": false,
    "publishConfig": {}
  }
```

## 2. name 字段

---

package.json 文件中最重要的就是 name 和 version 字段，这两项是必填的。名称和版本一起构成一个标识符，该标识符被认为是完全唯一的。对包的更改应该与对版本的更改一起进行。
name 必须小于等于 214 个字符，不能以.或\_开头，不能有大写字母，因为名称最终成为 URL 的一部分因此不能包含任何非 URL 安全字符。

npm 官方建议我们不要使用与核心节点模块相同的名称。不要在名称中加 js 或 node。如果需要可以使用 engines 来指定运行环境。
字段不能与其他模块名重复，我们可以执行以下命令查看模块名是否已经被使用：

```
npm view <packageName>
```

该名称会作为参数传递给 require，因此它应该是简短的，但也需要具有合理的描述性。

- 模块名会成为模块 url、命令行中的一个参数或者一个文件夹名称，任何非 url 安全的字符在模块名中都不能使用（我们可以使用 validate-npm-package-name 包来检测模块名是否合法）；
- 语义化模块名，可以帮助开发者更快的找到需要的模块，并且避免意外获取错误的模块；
- 若模块名称中存在一些符号，将符号去除后不得与现有的模块名重复，例如：由于 react-router-dom 已经存在，react.router.dom、reactrouterdom 都不可以再创建。

## 3. version 字段

---

version 一般的格式是 x.x.x, 并且需要遵循该规则。

package.json 文件中最重要的就是 name 和 version 字段，这两项是必填的。名称和版本一起构成一个标识符，该标识符被认为是完全唯一的。每次发布时 version 不能与已存在的一致。

- npm 包中的模块版本都需要遵循 SemVer 规范，该规范的标准版本号采用 X.Y.Z 的格式，其中 X、Y 和 Z 均为非负的整数，且禁止在数字前方补零：

  - X 是主版本号(major)：修改了不兼容的 API
  - Y 是次版本号(minor)：新增了向下兼容的功能
  - Z 为修订号(patch)：修正了向下兼容的问题

- 当某个版本改动比较大、并非稳定而且可能无法满足预期的兼容性需求时，我们可能要先发布一个先行版本。
- 先行版本号可以加到主版本号.次版本号.修订号的后面，通过 - 号连接一连串以句点分隔的标识符和版本编译信息：

  - 内部版本(alpha)
  - 公测版本(beta)
  - 正式版本的候选版本 rc（即 Release candiate）

- 我们可以执行以下命令查看模块的版本：

```
npm view <packageName> version # 查看某个模块的最新版本
npm view <packageName> versions # 查看某个模块的所有历史版本
```

## 4. description 字段

---

description 是一个字符串，用于编写描述信息。有助于人们在 npm 库中搜索的时候发现你的模块。

## 5. keywords 字段

---

keywords 是一个字符串组成的数组，有助于人们在 npm 库中搜索的时候发现你的模块。

## 6. homepage 字段

---

homepage 项目的主页地址。

## 7. bugs 字段

---

bugs 用于项目问题的反馈 issue 地址或者一个邮箱。

```
"bugs": {
  "url" : "https://github.com/owner/project/issues",
  "email" : "project@hostname.com"
}
```

## 8. license 字段

---

license 是当前项目的协议，让用户知道他们有何权限来使用你的模块，以及使用该模块有哪些限制。
"license" : "BSD-3-Clause"
复制代码

## 9. author 字段 contributors 字段

---

author 是具体一个人，contributors 表示一群人，他们都表示当前项目的共享者。同时每个人都是一个对象。具有 name 字段和可选的 url 及 email 字段。

```
"author": {
  "name" : "yindong",
  "email" : "yindong@xx.com",
  "url" : "https://zhiqianduan.com/"
}
```

也可以写成一个字符串

```
"author": "yindong yindong@xx.com (https://zhiqianduan.com/)"
```

## 10. files 字段

---

files 属性的值是一个数组，内容是模块下文件名或者文件夹名，如果是文件夹名，则文件夹下所有的文件也会被包含进来（除非文件被另一些配置排除了）

可以在模块根目录下创建一个.npmignore 文件，写在这个文件里边的文件即便被写在 files 属性里边也会被排除在外，这个文件的写法与.gitignore 类似。

## 11. main 字段

---

main 字段指定了加载的入口文件，require 导入的时候就会加载这个文件。这个字段的默认值是模块根目录下面的 index.js。

## 12. bin 字段

---

bin 项用来指定每个内部命令对应的可执行文件的位置。如果你编写的是一个 node 工具的时候一定会用到 bin 字段。
当我们编写一个 cli 工具的时候，需要指定工具的运行命令，比如常用的 webpack 模块，他的运行命令就是 webpack。

```
"bin": {
  "webpack": "bin/index.js",
}
```

当我们执行 webpack 命令的时候就会执行 bin/index.js 文件中的代码。

在模块以依赖的方式被安装，如果存在 bin 选项。在 node_modules/.bin/生成对应的文件，Npm 会寻找这个文件，在 node_modules/.bin/目录下建立符号链接。由于 node_modules/.bin/目录会在运行时加入系统的 PATH 变量，因此在运行 npm 时，就可以不带路径，直接通过命令来调用这些脚本。

所有 node_modules/.bin/目录下的命令，都可以用 npm run [命令]的格式运行。在命令行下，键入 npm run，然后按 tab 键，就会显示所有可以使用的命令。

- 用过 vue-cli，create-react-app 等脚手架的朋友们，不知道你们有没有好奇过，为什么安装这些脚手架后，就可以使用类似 vue create/create-react-app 之类的命令，其实这和 package.json 中的 bin 字段有关。
- bin 字段用来指定各个内部命令对应的可执行文件的位置。当 package.json 提供了 bin 字段后，即相当于做了一个命令名和本地文件名的映射。
- 当用户安装带有 bin 字段的包时，

  - 如果是全局安装，npm 将会使用符号链接把这些文件链接到/usr/local/node_modules/.bin/；
  - 如果是本地安装，会链接到./node_modules/.bin/。

- 举个 🌰，如果要使用 my-app-cli 作为命令时，可以配置以下 bin 字段：

```
"bin": {
  "my-app-cli": "./bin/cli.js"
}
```

- 上面代码指定，my-app-cli 命令对应的可执行文件为 bin 子目录下的 cli.js，因此在安装了 my-app-cli 包的项目中，就可以很方便地利用 npm 执行脚本：

```
"scripts": {
  start: 'node node_modules/.bin/my-app-cli'
}
```

- 咦，怎么看起来和 vue create/create-react-app 之类的命令不太像？原因：

  - 当需要 node 环境时就需要加上 node 前缀
  - 如果加上 node 前缀，就需要指定 my-app-cli 的路径 -> node_modules/.bin，否则 node my-app-cli 会去查找当前路径下的 my-app-cli.js，这样肯定是不对。

- 若要实现像 vue create/create-react-app 之类的命令一样简便的方式，则可以在上文提到的 bin 子目录下可执行文件 cli.js 中的第一行写入以下命令：

```
#!/usr/bin/env node
```

## 13. man 字段

---

man 用来指定当前模块的 man 文档的位置。

```
"man" :[ "./doc/calc.1" ]
```

## 14. directories 字段

---

directories 制定一些方法来描述模块的结构, 用于告诉用户每个目录在什么位置。

## 15. repository 字段

---

指定一个代码存放地址，对想要为你的项目贡献代码的人有帮助

```
"repository" : {
  "type" : "git",
  "url" : "https://github.com/npm/npm.git"
}
```

## 16. scripts 字段

---

scripts 指定了运行脚本命令的 npm 命令行缩写，比如 start 指定了运行 npm run start 时，所要执行的命令。

```
"scripts": {
  "start": "node ./start.js"
}
```

使用 scripts 字段可以快速的执行 shell 命令，可以理解为 alias。

scripts 可以直接使用 node_modules 中安装的模块，这区别于直接运行需要使用 npx 命令。

```
"scripts": {
  "build": "webpack"
}

// npm run build
// npx webpack
```

## 17. config 字段

---

config 字段用于添加命令行的环境变量。

```
{
  "name" : "yindong",
  "config" : { "port" : "8080" },
  "scripts" : { "start" : "node server.js" }
}
```

然后，在 server.js 脚本就可以引用 config 字段的值。

```
console.log(process.env.npm_package_config_port); // 8080
```

用户可以通过 npm config set 来修改这个值。

```
npm config set yindong:port 8000
```

## 18. dependencies 字段, devDependencies 字段

---

dependencies 字段指定了项目运行所依赖的模块，devDependencies 指定项目开发所需要的模块。

它们的值都是一个对象。该对象的各个成员，分别由模块名和对应的版本要求组成，表示依赖的模块及其版本范围。

当安装依赖的时候使用--save 参数表示将该模块写入 dependencies 属性，--save-dev 表示将该模块写入 devDependencies 属性。

```
"devDependencies": {
  "webpack": "^5.38.1",
}
```

对象的每一项通过一个键值对表示，前面是模块名称，后面是对应模块的版本号。版本号遵循“大版本.次要版本.小版本”的格式规定。

> 版本说明
>
> > 固定版本: 比如 5.38.1，安装时只安装指定版本。
> > 波浪号: 比如~5.38.1, 表示安装 5.38.x 的最新版本（不低于 5.38.1），但是不安装 5.39.x，也就是说安装时不改变大版本号和次要版本号。
> > 插入号: 比如 ˆ5.38.1, ，表示安装 5.x.x 的最新版本（不低于 5.38.1），但是不安装 6.x.x，也就是说安装时不改变大版本号。需要注意的是，如果大版本号为 0，则插入号的行为与波浪号相同，这是因为此时处于开发阶段，即使是次要版本号变动，也可能带来程序的不兼容。
> > latest: 安装最新版本。

## 19. peerDependencies 字段

---

当我们开发一个模块的时候，如果当前模块与所依赖的模块同时依赖一个第三方模块，并且依赖的是两个不兼容的版本时就会出现问题。

比如，你的项目依赖 A 模块和 B 模块的 1.0 版，而 A 模块本身又依赖 B 模块的 2.0 版。

大多数情况下，这不构成问题，B 模块的两个版本可以并存，同时运行。但是，有一种情况，会出现问题，就是这种依赖关系将暴露给用户。

最典型的场景就是插件，比如 A 模块是 B 模块的插件。用户安装的 B 模块是 1.0 版本，但是 A 插件只能和 2.0 版本的 B 模块一起使用。这时，用户要是将 1.0 版本的 B 的实例传给 A，就会出现问题。因此，需要一种机制，在模板安装的时候提醒用户，如果 A 和 B 一起安装，那么 B 必须是 2.0 模块。

peerDependencies 字段，就是用来供插件指定其所需要的主工具的版本。可以通过 peerDependencies 字段来限制，使用 myless 模块必须依赖 less 模块的 3.9.x 版本.

```
{
  "name": "myless",
  "peerDependencies": {
    "less": "3.9.x"
  }
}
```

注意，从 npm 3.0 版开始，peerDependencies 不再会默认安装了。就是初始化的时候不会默认带出。

## 20. bundledDependencies 字段

---

bundledDependencies 指定发布的时候会被一起打包的模块.

## 21. optionalDependencies 字段

---

如果一个依赖模块可以被使用， 同时你也希望在该模块找不到或无法获取时 npm 继续运行，你可以把这个模块依赖放到 optionalDependencies 配置中。这个配置的写法和 dependencies 的写法一样，不同的是这里边写的模块安装失败不会导致 npm install 失败。

## 22. engines 字段

---

engines 字段指明了该模块运行的平台，比如 Node 或者 npm 的某个版本或者浏览器。

```
{ "engines" : { "node" : ">=0.10.3 <0.12", "npm" : "~1.0.20" } }
```

## 23. os 字段

---

可以指定你的模块只能在哪个操作系统上运行

```
"os" : [ "darwin", "linux", "win32" ]
```

## 24. cpu 字段

---

限制模块只能在某种架构的 cpu 下运行

```
"cpu" : [ "x64", "ia32" ]
```

## 25. private 字段

---

如果这个属性被设置为 true，npm 将拒绝发布它，这是为了防止一个私有模块被无意间发布出去。

```
"private": true
```

## 26. publishConfig 字段

---

这个配置是会在模块发布时生效，用于设置发布用到的一些值的集合。如果你不想模块被默认标记为最新的，或者默认发布到公共仓库，可以在这里配置 tag 或仓库地址。

通常 publishConfig 会配合 private 来使用，如果你只想让模块被发布到一个特定的 npm 仓库，如一个内部的仓库。

```
"private": true,
"publishConfig": {
  "tag": "1.0.0",
  "registry": "https://registry.npmjs.org/",
  "access": "public"
}
```

## 27. preferGlobal 字段

---

preferGlobal 的值是布尔值，表示当用户不将该模块安装为全局模块时（即不用–global 参数），要不要显示警告，表示该模块的本意就是安装为全局模块。

```
"preferGlobal": false
```

## 28. browser 字段

---

browser 指定该模板供浏览器使用的版本。Browserify 这样的浏览器打包工具，通过它就知道该打包那个文件。

```
"browser": {
  "tipso": "./node_modules/tipso/src/tipso.js"
},
```

参考来源

- [1] [npm package.json](https://link.juejin.cn/?target=http%3A%2F%2Fcaibaojian.com%2Fnpm%2Ffiles%2Fpackage.json.html)
- [2] [javaScript 标准参考教程](https://link.juejin.cn/?target=http%3A%2F%2Fjavascript.ruanyifeng.com%2Fnodejs%2Fpackagejson.html)

转自：[【掘金】](https://juejin.cn/post/6987179395714646024) 你真的了解 package.json 吗？来看看吧，这可能是最全的 package 解析 "隐冬"
