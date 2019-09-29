---
layout: post
title: '图解Https与Http'
date: 2019-03-15
author: lucklcy
cover: '/assets/img/http-vs-https.png'
tags: [http, https]
---

> 本文原出处于**_[知乎](https://zhuanlan.zhihu.com/p/57142784)_**,原作者：**_程序员小灰_**

![img](/assets/img/carton_http_s/001.jpg)
![img](/assets/img/carton_http_s/002.jpg)
![img](/assets/img/carton_http_s/003.jpg)
![img](/assets/img/carton_http_s/004.jpg)
![img](/assets/img/carton_http_s/005.jpg)
![img](/assets/img/carton_http_s/006.jpg)
![img](/assets/img/carton_http_s/007.jpg)
![img](/assets/img/carton_http_s/008.jpg)
![img](/assets/img/carton_http_s/009.jpg)
**什么是 HTTP 协议？**

HTTP 协议全称 Hyper Text Transfer Protocol，翻译过来就是超文本传输协议，位于 TCP/IP 四层模型当中的应用层。
![img](/assets/img/carton_http_s/010.jpg)
**HTTP 协议通过请求/响应的方式，在客户端和服务端之间进行通信。**
![img](/assets/img/carton_http_s/011.jpg)
这一切看起来很美好，但是 HTTP 协议有一个致命的缺点：**_不够安全_**。

**HTTP 协议**的信息传输完全以明文方式，不做任何加密，相当于是在网络上“裸奔”。这样会导致什么问题呢？让我们打一个比方：

小灰是客户端，小灰的同事小红是服务端，有一天小灰试图给小红发送请求。
![img](/assets/img/carton_http_s/012.jpg)
但是，由于传输信息是明文，这个信息有可能被某个中间人恶意截获甚至篡改。这种行为叫做**中间人攻击**。
![img](/assets/img/carton_http_s/013.jpg)
![img](/assets/img/carton_http_s/014.jpg)
![img](/assets/img/carton_http_s/015.jpg)
**如何进行加密呢？**

小灰和小红可以事先约定一种**_对称加密_**方式，并且约定一个随机生成的密钥。后续的通信中，信息发送方都使用密钥对信息加密，而信息接收方通过同样的密钥对信息解密。
![img](/assets/img/carton_http_s/016.jpg)
![img](/assets/img/carton_http_s/017.jpg)
这样做是不是就绝对安全了呢？并不是。

虽然我们在后续的通信中对明文进行了加密，但是第一次约定加密方式和密钥的通信仍然是明文，如果第一次通信就已经被拦截了，那么密钥就会泄露给中间人，中间人仍然可以解密后续所有的通信内容。
![img](/assets/img/carton_http_s/018.jpg)
这可怎么办呢？别担心，我们可以使用**_非对称加密_**，为密钥的传输做一层额外的保护。

非对称加密的一组秘钥对中，包含一个**公钥**和一个**私钥**。明文既可以用*公钥加密，用私钥解密；也可以用私钥加密，用公钥解密*。

在小灰和小红建立通信的时候，小红首先把自己的公钥 Key1 发给小灰：
![img](/assets/img/carton_http_s/019.jpg)
收到小红的公钥以后，小灰自己生成一个用于对称加密的密钥 Key2，并且用刚才接收的公钥 Key1 对 Key2 进行加密（这里有点绕），发送给小红：
![img](/assets/img/carton_http_s/020.jpg)
小红利用自己非对称加密的私钥，解开了公钥 Key1 的加密，获得了 Key2 的内容。从此以后，两人就可以利用 Key2 进行对称加密的通信了。
![img](/assets/img/carton_http_s/021.jpg)
在通信过程中，即使中间人在一开始就截获了公钥 Key1，由于不知道私钥是什么，也无从解密。
![img](/assets/img/carton_http_s/022.jpg)
![img](/assets/img/carton_http_s/023.jpg)
是什么坏主意呢？中间人虽然不知道小红的私钥是什么，但是在截获了小红的公钥 Key1 之后，却可以偷天换日，自己另外生成一对公钥私钥，把自己的公钥 Key3 发送给小灰。
![img](/assets/img/carton_http_s/024.jpg)
小灰不知道公钥被偷偷换过，以为 Key3 就是小红的公钥。于是按照先前的流程，用 Key3 加密了自己生成的对称加密密钥 Key2，发送给小红。

这一次通信再次被中间人截获，中间人先用自己的私钥解开了 Key3 的加密，获得 Key2，然后再用当初小红发来的 Key1 重新加密，再发给小红。
![img](/assets/img/carton_http_s/025.jpg)
这样一来，两个人后续的通信尽管用 Key2 做了对称加密，但是中间人已经掌握了 Key2，所以可以轻松进行解密。
![img](/assets/img/carton_http_s/026.jpg)
![img](/assets/img/carton_http_s/027.jpg)
是什么解决方案呢？难道再把公钥进行一次加密吗？这样只会陷入鸡生蛋蛋生鸡，永无止境的困局。

这时候，我们有必要引入第三方，一个权威的证书颁发机构**（CA）**来解决。

到底什么是**证书**呢？**证书**包含如下信息：
![img](/assets/img/carton_http_s/028.jpg)
为了便于说明，我们这里做了简化，只列出了一些关键信息。至于这些证书信息的用处，我们看看具体的通信流程就能够弄明白了。

流程如下：

1.作为服务端的小红，首先把自己的公钥发给证书颁发机构，向证书颁发机构申请证书。
![img](/assets/img/carton_http_s/029.jpg) 2.证书颁发机构自己也有一对公钥私钥。机构利用自己的私钥来加密 Key1，并且通过服务端网址等信息生成一个证书签名，证书签名同样经过机构的私钥加密。证书制作完成后，机构把证书发送给了服务端小红。
![img](/assets/img/carton_http_s/030.jpg) 3.当小灰向小红请求通信的时候，小红不再直接返回自己的公钥，而是把自己申请的证书返回给小灰。
![img](/assets/img/carton_http_s/031.jpg) 4.小灰收到证书以后，要做的第一件事情是验证证书的真伪。需要说明的是，**_各大浏览器和操作系统已经维护了所有权威证书机构的名称和公钥_**。所以小灰只需要知道是哪个机构颁布的证书，就可以从本地找到对应的机构公钥，解密出证书签名。

接下来，小灰按照同样的签名规则，自己也生成一个证书签名，如果两个签名一致，说明证书是有效的。

验证成功后，小灰就可以放心地再次利用机构公钥，解密出服务端小红的公钥 Key1。
![img](/assets/img/carton_http_s/032.jpg) 5.像之前一样，小灰生成自己的对称加密密钥 Key2，并且用服务端公钥 Key1 加密 Key2，发送给小红。
![img](/assets/img/carton_http_s/033.jpg) 6.最后，小红用自己的私钥解开加密，得到对称加密密钥 Key2。于是两人开始用 Key2 进行对称加密的通信。
![img](/assets/img/carton_http_s/034.jpg)
在这样的流程下，我们不妨想一想，中间人是否还具有使坏的空间呢？
![img](/assets/img/carton_http_s/035.jpg)
![img](/assets/img/carton_http_s/036.jpg)
![img](/assets/img/carton_http_s/037.jpg)
![img](/assets/img/carton_http_s/039.jpg)
![img](/assets/img/carton_http_s/040.jpg)
![img](/assets/img/carton_http_s/041.jpg)
![img](/assets/img/carton_http_s/042.jpg)
![img](/assets/img/carton_http_s/043.jpg)
![img](/assets/img/carton_http_s/044.jpg)
