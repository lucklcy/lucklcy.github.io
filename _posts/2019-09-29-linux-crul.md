---
layout: post
title: 'Curl Command'
date: 2019-09-29
author: lucklcy
cover: '/assets/img/linux-commands.png'
tags: linux
---

# Curl Command

### Curl 指令基本介紹與常見用法

> Curl 是一個在 Linux 上用來透過 HTTP Protocol（HTTP HyperText Transfer Protocol 定義存取網路資源的協定，讓我們可以使用 client / server 模式來取得網路資源）下載和上傳檔案的指令（比起 wget 只能下載強大許多）。它基本的指令格式如下：

```
curl [options] [URL...]
```

打開終端機（terminal）然後，curl 後面加網址，就會在終端機內顯示回傳的 response，可能是 HTML、JSON 或是 XML 等格式，根據輸入的 URL 內容而定。

<pre><code class="language-js">curl https://www.baidu.com</code></pre>

#### 下载：

下載：可以使用 -o 搭配欲下載的檔名和網址;

<pre><code class="language-js">curl -o vue.png https://cn.vuejs.org/images/logo.png</code></pre>

若是使用 -O 則可以直接使用下載網址的檔案檔名來命名下載的檔案;

<pre><code class="language-js">curl -O https://cn.vuejs.org/images/logo.png</code></pre>

有可能在下載過程中被中斷，若是想要從中斷的地方繼續的話，可以使用 -C 選項：

<pre><code class="language-js">curl -C - -O http://releases.ubuntu.com/18.04/ubuntu-18.04-desktop-amd64.iso</code></pre>

若希望可以跟隨著網址 301/302 redirect 的話，可以使用 -L 選項：

<pre><code class="language-js">curl -L http://google.com</code></pre>

若我們要追蹤整個 curl 過程，並將結果存入 debugdump.txt 檔案可以使用 --trace-ascii 指令：

<pre><code class="language-js">curl --trace-ascii debugdump.txt http://www.example.com/</code></pre>

## 使用 Curl 來進行 HTTP Request

除了簡易的下載檔案或是取得網頁內容外，Curl 還支援各種不同 HTTP 請求方法（HTTP method），以下列出常用指令和選項參數：

| 参数                                         | 详情                                       |
| -------------------------------------------- | ------------------------------------------ |
| -X/--request [GET\|POST\|PUT\|DELETE\|PATCH] | 使用指定的 http method 來發出 http request |
| -H/--header                                  | 設定 request 裡所攜帶的 header             |
| -i/--include                                 | 在 output 顯示 response 的 header          |
| -d/--data                                    | 攜帶 HTTP POST Data                        |
| -v/--verbose                                 | 輸出更多的訊息方便 debug                   |
| -u/--user                                    | 攜帶使用者帳號、密碼                       |
| -b/--cookie                                  | 攜帶 cookie（可以是參數或是檔案位置）      |

#### 1. GET

簡易一個 URL 版本，可以攜帶 query string 參數取得網路資源:

<pre><code class="language-js">$ curl https://example.com?q1=123&q2=abc</code></pre>

在同一個指令使用多個 URL：

<pre><code class="language-js">$ curl http://example1.com http://example2.com</code></pre>

#### 2. Form POST

一般而言我們 Form 表單的 HTML 會長這樣：

<pre><code class="language-js">&lt;form method="POST" action="form.php"&gt;
   &lt;input type=text name="email"&gt;
   &lt;input type=submit name=press value=" OK "&gt;
&lt;/form&gt;</code></pre>

由於 Form post 是使用 application/x-www-form-urlencoded Content-Type，所以傳遞的值需要編碼：

<pre><code class="language-js">＄ curl -X POST --data "email=test@example.com&press=%20OK%20" http://
www.example.com/form.php</code></pre>

#### 3. File Upload POST

另一種常見 Form 表單是有涉及檔案上傳（使用 multipart/form-data Content-Type）：

<pre><code class="language-js">&lt;form method="POST" enctype='multipart/form-data' action="upload.php"&gt;
 &lt;input type=file name=upload&gt;
 &lt;input type=submit name=press value="OK"&gt;
&lt;/form&gt;</code></pre>

可以看到指令會需要攜帶 upload 檔案：

<pre><code class="language-js">curl -X POST -F 'file=@./upload.txt' http://www.example.com/upload.php</code></pre>

#### 4. 常見 Restful CRUD 指令：

- GET 單一和全部資源:

<pre><code class="language-js">$ curl -X GET "http://www.example.com/api/resources"
$ curl -X GET "http://www.example.com/api/resources/1"</code></pre>

- POST JSON 資料：

<pre><code class="language-js">$ curl -X POST -H "Content-Type: application/json" -d '{"status" : 
false, "name" : "Jack"}' "http://www.example.com/api/resources"</code></pre>

- PUT JSON 資料：

<pre><code class="language-js">curl -X PUT -H "Content-Type: application/json" -d '{"status" : false 
}' "http://www.example.com/api/resources"</code></pre>

- DELETE 資源：

<pre><code class="language-js">$ curl -X DELETE "http://www.example.com/api/resources/1"</code></pre>

- 攜帶 cookie【在指令中輸入 cookie】：

<pre><code class="language-js">$ curl --cookie "name=Jack" http://www.example.com</code></pre>

- 攜帶 cookie【從檔案讀取 cookie】：

<pre><code class="language-js">$ curl --cookie stored_cookies_file_path http://www.example.com</code></pre>

- 攜帶 User Agent

<pre><code class="language-js">$ curl --user-agent "Mozilla/5.0 (compatible; MSIE 5.01; Windows NT 5.0
)" http://www.example.com</code></pre>

- Basic Authentication【若所存取網頁有使用 Basic Authentication，可以攜帶 --user username:password 來通過驗證：】

<pre><code class="language-js">$ curl -i --user secret:vary_secret http://www.example.com/api/resources</code></pre>
