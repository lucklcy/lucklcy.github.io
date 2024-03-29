---
layout: post
title: "常用Nginx配置"
date: 2019-03-14
author: lucklcy
cover: "/assets/img/nginx-logo.png"
tags: nginx
---

# nginx 配置文件 nginx.conf 超详细讲解

**[正则表达式文档](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/RegExp)**

**[Nginx 官方文档](http://www.nginx.cn/doc/)**

## 指令上下文

<pre><code class="language-text">nginx.conf 中的配置信息，根据其逻辑上的意义，对它们进行了分类，也就是分成了多个作用
域，或者称之为配置指令上下文。不同的作用域含有一个或者多个配置项。

当前 Nginx 支持的几个指令上下文：
  
  main: Nginx 在运行时与具体业务功能（比如 http 服务或者 email 服务代理）无关
  的一些参数，比如工作进程数，运行的身份等。

  http: 与提供 http 服务相关的一些配置参数。例如：是否使用 keepalive 啊，是否
  使用 gzip 进行压缩等。

  server: http 服务上支持若干虚拟主机。每个虚拟主机一个对应的 server 配置项，
  配置项里面包含该虚拟主机相关的配置。在提供 mail 服务的代理时，也可以建立若干 
  server，每个 server 通过监听的地址来区分。
  
  location: http 服务中，某些特定的 URL 对应的一系列配置项。

  mail: 实现 email 相关的 SMTP/IMAP/POP3 代理时，共享的一些配置项（因为可能
  实现多个代理，工作在多个监听地址上）。

  指令上下文，可能有包含的情况出现。例如：通常 http 上下文和 mail 上下文一定是
  出现在 main 上下文里的。在一个上下文里，可能包含另外一种类型的上下文多次。例如：
  如果 http 服务，支持了多个虚拟主机，那么在 http 上下文里，就会出现多个 server 
  上下文。
</code></pre>

## nginx 基本知识

### 正则表达式匹配，其中：

<pre><code class="language-conf">~ 为区分大小写匹配
~* 为不区分大小写匹配
!~和!~*分别为区分大小写不匹配及不区分大小写不匹配
^~ 如果把这个前缀用于一个常规字符串,那么告诉nginx 如果路径匹配那么不测试
正则表达式。
</code></pre>

### 文件及目录匹配，其中：

<pre><code class="language-conf">-f和!-f用来判断是否存在文件
-d和!-d用来判断是否存在目录
-e和!-e用来判断是否存在文件或目录
-x和!-x用来判断文件是否可执行
</code></pre>

### 匹配优先级：

<pre><code class="language-conf">多个 location 配置的情况下匹配顺序为:
精确匹配 =
前缀匹配 ^~
按文件中顺序的正则匹配
匹配不带任何修饰的前缀匹配
交给/通用匹配
当有匹配成功时候，停止匹配，按当前匹配规则处理请求
</code></pre>

### rewrite 指令的最后一项参数为 flag 标记，flag 标记有：

<pre><code class="language-conf">1.last 相当于apache里面的[L]标记，表示rewrite。
    last 标记在本条 rewrite 规则执行完毕后，会对其所在 server{......}标签
    重新发起请求使用 alias 指令必须用 last 标记;
2.break 本条规则匹配完成后，终止匹配，不再匹配后面的规则。
    使用 proxy_pass 指令时，需要使用 break 标记。
    使用 last 和 break 实现 URI 重写，浏览器地址栏不变。
3.redirect 返回 302 临时重定向，浏览器地址会显示跳转后的 URL 地址。
4.permanent 返回 301 永久重定向，浏览器地址会显示跳转后的 URL 地址。
</code></pre>

### NginxRewrite 规则相关指令

<pre><code class="language-conf">1.break指令
    使用环境：server,location,if;
    该指令的作用是完成当前的规则集，不再处理rewrite指令。
2.if指令
　  使用环境：server,location
　  该指令用于检查一个条件是否符合，如果条件符合，则执行大括号内的语句。If指令
  不支持嵌套，不支持多个条件&&和||处理。
3.return 指令
　　语法：return code ;
　　使用环境：server,location,if;
　　该指令用于结束规则的执行并返回状态码给客户端。
　　示例：如果访问的 URL 以".sh"或".bash"结尾，则返回 403 状态码
　　 location ~ .*\.(sh|bash)?$
　　{
　　　　 return 403;
　　}
4.rewrite 指令
　　语法：rewrite regex replacement flag
　　使用环境：server,location,if
　　该指令根据表达式来重定向 URI，或者修改字符串。指令根据配置文件中的顺序
  来执行。注意重写表达式只对相对路径有效。如果你想配对主机名，你应该
  使用 if 语句，示例如下：
　　if ( $host ~* www\.(.*) ){
　　　 set  host_without_www $1;
　　　 rewrite  ^(.*)$ http://$host_without_www$1 permanent;
　  }
5.set 指令
　　语法：set variable value ;
　　默认值:none;
　　使用环境：server,location,if;
　　该指令用于定义一个变量，并给变量赋值。变量的值可以为文本、变量以及
  文本变量的联合。
　　示例：set $varname "hello world";
6.Uninitialized_variable_warn 指令
　　语法：uninitialized_variable_warnon|off
　　使用环境：http,server,location,if
　　该指令用于开启和关闭未初始化变量的警告信息，默认值为开启。
</code></pre>

### Nginx 的 Rewrite 规则编写实例

#### 当访问的文件和目录不存在时，重定向到某个 php 文件

<pre><code class="language-conf">if ( !-e $request_filename ) {
  rewrite ^/(.*)$ index.php last;
}</code></pre>

#### 目录对换 /123456/xxxx ====> /xxxx?id=123456

<pre><code class="language-conf">rewrite ^/(\d+)/(.+)/ /$2?id=$1 last;</code></pre>

#### 如果客户端使用的是 IE 浏览器，则重定向到/ie 目录下

<pre><code class="language-conf">if( $http_user_agent ~ MSIE) {
  rewrite ^(.*)$ /ie/\$1 break;
}</code></pre>

#### 禁止访问多个目录

<pre><code class="language-conf">location ~ ^/(cron|templates)/ {
  deny all;
  break;
}</code></pre>

#### 禁止访问以/data 开头的文件

<pre><code class="language-conf">location ~ ^/data {
  deny all;
}</code></pre>

#### 禁止访问以.sh,.flv,.mp3 为文件后缀名的文件

<pre><code class="language-conf">location ~ /.*\.(sh|flv|mp3)$ {
  return 403;
}</code></pre>

#### 设置某些类型文件的浏览器缓存时间

<pre><code class="language-conf">location ~ /.*\.(gif|jpg|jpeg|png|bmp|swf)$ {
  expires 30d;
}
location ~ /.*\.(js|css)$ {
  expires 1h;
}</code></pre>

#### 给 favicon.ico 和 robots.txt 设置过期时间; # 这里为 favicon.ico 为 99 天,robots.txt 为 7 天并不记录 404 错误日志

<pre><code class="language-conf">location ~(favicon.ico) {
  log_not_found off;
  expires 99d;
  break;
}
location ~(robots.txt) {
  log_not_found off;
  expires 7d;
  break;
}</code></pre>

#### 设定某个文件的过期时间;这里为 600 秒，并不记录访问日志

<pre><code class="language-conf">location ^~ /html/scripts/loadhead_1.js {
  access_log off;
  root /opt/lampp/htdocs/web;
  expires 600;
  break;
}</code></pre>

#### 文件反盗链并设置过期时间 # 这里的 return412 为自定义的 http 状态码，默认为 403，方便找出正确的盗链的请求

<pre><code class="language-conf">location ~_^.+\.(jpg|jpeg|gif|png|swf|rar|zip|css|js)$ {
  valid_referers none blocked _.linuxidc.com\*.linuxidc.net 
  localhost 208.97.167.194;
  if ($invalid_referer)
  { # 显示一张防盗链图片
      rewrite ^/ http://img.linuxidc.net/leech.gif;
      return 412;
  break;
  } # 不记录访问日志，减轻压力
  access_log off;
  root /opt/lampp/htdocs/web; # 所有文件 3 天的浏览器缓存
  expires 3d;
  break;
}</code></pre>

#### 只允许固定 ip 访问网站，并加上密码

<pre><code class="language-conf">root /opt/htdocs/www;
allow 208.97.167.194;
allow 222.33.1.2;
allow 231.152.49.4;
deny all;
auth_basic “C1G_ADMIN”;
auth_basic_user_file htpasswd;</code></pre>

#### 将多级目录下的文件转成一个文件，增强 seo 效果 # /job-123-456-789.html 指向/job/123/456/789.html

<pre><code class="language-conf">rewrite ^/job-([0-9]+)-([0-9]+)-([0-9]+)\.html$ 
  /job/$1/$2/jobshow_$3.html last;</code></pre>

#### 文件和目录不存在的时候重定向：

<pre><code class="language-conf">if (!-e \$request_filename) {
  proxy_pass http://127.0.0.1;
}</code></pre>

#### 将根目录下某个文件夹指向 2 级目录 # 如/shanghaijob/ 指向 /area/shanghai/ # 如果你将 last 改成 permanent，那么浏览器地址栏显是/location/shanghai/

<pre><code class="language-conf">rewrite ^/([0-9a-z]+)job/(._)$ /area/$1/$2 last;
# 上面例子有个问题是访问/shanghai时将不会匹配
rewrite ^/([0-9a-z]+)job$ /area/\$1/ last;
rewrite ^/([0-9a-z]+)job/(._)$ /area/$1/$2 last;
# 这样/shanghai 也可以访问了，但页面中的相对链接无法使用，
# 如./list_1.html真实地址是/area/shanghia/list_1.html会变成/list_1.html,
  导致无法访问。
# 那我加上自动跳转也是不行咯
# (-d $request_filename)它有个条件是必需为真实目录，而我的 rewrite 不是的，
  所以没有效果。
if (-d $request_filename) {
  rewrite ^/(.*)([^/])$ http://$host/$1$2/permanent;
} # 知道原因后就好办了，让我手动跳转吧
rewrite ^/([0-9a-z]+)job$ /$1job/ permanent;
rewrite ^/([0-9a-z]+)job/(.\*)$ /area/$1/\$2 last;</code></pre>

#### 域名跳转

<pre><code class="language-conf">server {
  listen 80;
  server_name jump.linuxidc.com;
  ndex index.html index.htm index.php;
  root /opt/lampp/htdocs/www;
  rewrite ^/ http://www.linuxidc.com/;
  access_log off;
}</code></pre>

#### 多域名转向

<pre><code class="language-conf">server_name www.linuxidc.com www.linuxidc.net;
index index.html index.htm index.php;
root /opt/lampp/htdocs;
if ($host ~ "linuxidc\.net") {
    rewrite ^(.*) http://www.linuxidc.com$1permanent;
}</code></pre>

### nginx 全局变量

<pre><code class="language-conf">01. $args: 该变量中存放了请求URL中的请求指令。
    比如 http://127.0.0.1:3001?arg1=value1&arg2=value2 
    中的"arg1=value1&arg2=value2"。

02. $content_length: 该变量中存放了请求头中的Content-length字段。

03. $content_type: 该变量中存放了请求头中的 Content-type字段。

04. $document_root: 该变量中存放了针对当前请求的根路径。

05. $document_uri: 该变量中存放了请求的当前URI, 但是不包括请求指令。
    比如 http://xxx.abc.com/home/1?arg1=value1&arg2=value2 中的 "/home/1"

06. $host: 变量中存放了请求的URL中的主机部分字段，比如
    http://xxx.abc.com:8080/home中的 xxx.abc.com.

07. $http_host: 该变量与$host唯一区别带有端口号：
    比如上面的是 xxx.abc.com:8080

08. $http_user_agent: 变量中存放客户端的代理信息。

09. $http_cookie, 该变量中存放客户端的cookie信息。

10. $remote_addr 该变量中存放客户端的地址。

11. $remote_port 该变量中存放了客户端与服务器建立连接的端口号。

12. $remote_user 变量中存放客户端的用户名。

13. $request_body_file 变量中存放了发给后端服务器的本地文件资源的名称。

14. $request_method 变量中存放了客户端的请求方式，比如 'GET'、'POST'等。

15. $request_filename 变量中存放了当前请求的资源文件的路径名。

16. $request_uri 变量中存放了当前请求的URI，并且带请求指令。

17. $query_string 和变量$args含义一样。

18. $scheme 变量中存放了客户端请求使用的协议，比如 'http', 'https'等。

19. $server_protocol 变量中存放了客户端请求协议的版本, 
    比如 'HTTP/1.0'、'HTTP/1.1' 等。
</code></pre>

### 根设置

<pre><code class="language-conf"># nginx进程,一般设置为和cpu核数一样
  worker_processes 4;

# 错误日志存放目录
# 全局错误日志定义类型，[ debug | info | notice | warn | error | crit ]
  error_log  /data1/logs/error.log  crit;

# 运行用户，默认即是nginx，可不设置
  user nginx

# 进程pid存放位置
  pid /application/nginx/nginx.pid;

# 一个nginx进程打开的最多文件描述符数目，理论值应该是最多打开文件数（系统的
  值ulimit -n）与nginx进程数相除，但是nginx分配请求并不均匀，所以建议与
  ulimit -n的值保持一致。
  worker_rlimit_nofile 51200;

# cpu亲和力配置，让不同的进程使用不同的cpu
  worker_cpu_affinity 0001 0010 0100 1000 0001 00100100 1000;

# 工作模式及连接数上限
  events {
    # 参考事件模型，use [ kqueue | rtsig | epoll | /dev/poll | select | 
    poll ];epoll模型是Linux 2.6以上版本内核中的高性能网络I/O模型，如果跑在
    FreeBSD上面，就用kqueue模型。
      use epoll;
    # 单个进程最大连接数（最大连接数=连接数*进程数）
      worker_connections 1024;
  }
</code></pre>

### http 设置

<pre><code class="language-conf">http {
  # 文件扩展名与类型映射表
    include mime.types;

  # 默认文件类型
    default_type application/octet-stream;

  # limit模块，可防范一定量的DDOS攻击，用来存储session会话的状态，如下是为
    session分配一个名为one的10M的内存存储区，限制了每秒只接受一个ip的一次
    请求 1r/s
    limit_req_zone $binary_remote_addr zone=one:10m rate=1r/s;
    limit_conn_zone $binary_remote_addr zone=addr:10m;

  # 第三方模块lua防火墙
    lua_need_request_body on;
   #lua_shared_dict limit 50m;
    lua_package_path "/application/nginx/conf/waf/?.lua";
    init_by_lua_file "/application/nginx/conf/waf/init.lua";
    access_by_lua_file "/application/nginx/conf/waf/access.lua";

  # 设定请求缓存

  # 服务器名字的hash表大小
    server_names_hash_bucket_size 128;
  # 上传文件大小限制
    client_header_buffer_size 512k;
  # 设定请求头缓存
    large_client_header_buffers 4 512k;
  # 设定请求体缓存
    client_max_body_size 100m;

  # 隐藏响应header和错误通知中的版本号
    server_tokens off;

  # 开启高效文件传输模式，sendfile指令指定nginx是否调用sendfile函数来输出文件，
    对于普通应用设为 on，如果用来进行下载等应用磁盘IO重负载应用，可设置为off，以
    平衡磁盘与网络I/O处理速度，降低系统的负载。注意：如果图片显示不正常把这个改成
    off。
    sendfile on;

  # 开启目录列表访问，合适下载服务器，默认关闭。
    autoindex on;

  # 激活tcp_nopush参数可以允许把httpresponse header和文件的开始放在一个文件里
    发布，积极的作用是减少网络报文段的数量
    tcp_nopush     on;

  # 激活tcp_nodelay，内核会等待将更多的字节组成一个数据包，从而提高I/O性能
    tcp_nodelay on;</code></pre>

### tcp_nopush

- linux 下是 tcp_cork,上面的意思就是说，当使用 sendfile 函数时，tcp_nopush 才起作用，它和指令 tcp_nodelay 是互斥的。tcp_cork 是 linux 下 tcp/ip 传输的一个标准了，这个标准的大概的意思是，一般情况下，在 tcp 交互的过程中，当应用程序接收到数据包后马上传送出去，不等待，而 tcp_cork 选项是数据包不会马上传送出去，等到数据包最大时，一次性的传输出去，这样有助于解决网络堵塞，已经是默认了。
- 也就是说 tcp_nopush = on 会设置调用 tcp_cork 方法，这个也是默认的，结果就是数据包不会马上传送出去，等到数据包最大时，一次性的传输出去，这样有助于解决网络堵塞。
- 以快递投递举例说明一下（以下是我的理解，也许是不正确的），当快递东西时，快递员收到一个包裹，马上投递，这样保证了即时性，但是会耗费大量的人力物力，在网络上表现就是会引起网络堵塞，而当快递收到一个包裹，把包裹放到集散地，等一定数量后统一投递，这样就是 tcp_cork 的选项干的事情，这样的话，会最大化的利用网络资源，虽然有一点点延迟。
- 对于 nginx 配置文件中的 tcp_nopush，默认就是 tcp_nopush,不需要特别指定，这个选项对于 www，ftp 等大文件很有帮助

### tcp_nodelay

- TCP_NODELAY 和 TCP_CORK 基本上控制了包的“Nagle 化”，Nagle 化在这里的含义是采用 Nagle 算法把较小的包组装为更大的帧。 John Nagle 是 Nagle 算法的发明人，后者就是用他的名字来命名的，他在 1984 年首次用这种方法来尝试解决福特汽车公司的网络拥塞问题（欲了解详情请参看 IETF RFC 896）。
  他解决的问题就是所谓的 silly window syndrome，中文称“愚蠢窗口症候群”，具体含义是，因为普遍终端应用程序每产生一次击键操作就会发送一个包，而典型情况下一个包会拥有一个字节的数据载荷以及 40 个字节长的包头，于是产生 4000%的过载，很轻易地就能令网络发生拥塞。
- Nagle 化后来成了一种标准并且立即在因特网上得以实现。它现在已经成为缺省配置了，但在我们看来，有些场合下把这一选项关掉也是合乎需要的。
- 现在让我们假设某个应用程序发出了一个请求，希望发送小块数据。我们可以选择立即发送数据或者等待产生更多的数据然后再一次发送两种策略。如果我们马上发送数据，那么交互性的以及客户/服务器型的应用程序将极大地受益。如果请求立即发出那么响应时间也会快一些。以上操作可以通过设置套接字的 TCP_NODELAY = on 选项来完成，这样就禁用了 Nagle 算法。
- 另外一种情况则需要我们等到数据量达到最大时才通过网络一次发送全部数据，这种数据传输方式有益于大量数据的通信性能，典型的应用就是文件服务器。应用 Nagle 算法在这种情况下就会产生问题。
- 但是，如果你正在发送大量数据，你可以设置 TCP_CORK 选项禁用 Nagle 化，其方式正好同 TCP_NODELAY 相反（TCP_CORK 和 TCP_NODELAY 是互相排斥的）。

<pre><code class="language-conf"># FastCGI相关参数：为了改善网站性能：减少资源占用，提高访问速度
  fastcgi_connect_timeout 300;
  fastcgi_send_timeout 300;
  fastcgi_read_timeout 300;
  fastcgi_buffer_size 64k;
  fastcgi_buffers 4 64k;
  fastcgi_busy_buffers_size 128k;
  fastcgi_temp_file_write_size 128k;

# 连接超时时间，单位是秒
  keepalive_timeout 60;

# 开启Gzip

# 开启gzip压缩功能
  gzip on；

# 设置允许压缩的页面最小字节数，页面字节数从header头的Content-Length中获取。
  默认值是0，表示不管页面多大都进行压缩。建议设置成大于1K。
  如果小于1K可能会越压越大。
  gzip_min_length  1k;

# 压缩缓冲区大小。表示申请4个单位为16K的内存作为压缩结果流缓存，默认值是申请与
  原始数据大小相同的内存空间来存储gzip压缩结果。
  gzip_buffers  4  16k;

# 压缩版本（默认1.1，前端为squid2.5时使用1.0）用于设置识别HTTP协议版本，默认
  是1.1，目前大部分浏览器已经支持GZIP解压，使用默认即可。
  gzip_http_version  1.1;

# 压缩比率。用来指定GZIP压缩比，1压缩比最小，处理速度最快；9压缩比最大，传输速度快，
  但处理最慢，也比较消耗cpu资源。
  gzip_comp_level 6;

# 用来指定压缩的类型，默认就已经包含text/html，所以下面就不用再写了，写上去也不会
  有问题，但是会有一个warn。
  gzip_types text/plain application/x-javascript 
             application/javascript text/css application/xml 
             text/javascript application/x-httpd-php 
             application/javascript application/json;

# vary header支持。该选项可以让前端的缓存服务器缓存经过GZIP压缩的页面，例如用
  gzip_vary on;

# 开启限制IP连接数的时候需要使用
  limit_zone crawler $binary_remote_addr 10m;

# 开启ssi支持，默认是off
  ssi on;
  ssi_silent_errors on;

# 设置日志模式
  log_format access '$remote_addr - $remote_user [$time_local] 
        "$request" '  '$status $body_bytes_sent "$http_referer" '
        '"$http_user_agent" $http_x_forwarded_for';

# 定义本虚拟主机的访问日志
  access_log /var/log/nginx/ha97access.log access;

# 反向代理负载均衡设定部分

# upstream表示负载服务器池，定义名字为backend_server的服务器池
  upstream backend_server {
    server   10.254.244.20:81 weight=1 max_fails=2 fail_timeout=30s;
    server   10.254.242.40:81 weight=1 max_fails=2 fail_timeout=30s;
    server   10.254.245.19:81 weight=1 max_fails=2 fail_timeout=30s;
    server   10.254.243.39:81 weight=1 max_fails=2 fail_timeout=30s;
    # fail_timeout 定义的时间段内连接该主机的失败次数，以此来断定 fail_timeout 
      定义的时间段内该主机是否可用。默认情况下这个数值设置为 1。零值的话禁用这个数
      量的尝试。
    # max_fail 设置在指定时间内连接到主机的失败次数，超过该次数该主机被认为不可用。
      这里是在30s内尝试2次失败即认为主机不可用！
    # weight是权重，可以根据机器配置定义权重。weigth参数表示权值，权值越高被分配
      到的几率越大。
  }</code></pre>

### 基于域名的虚拟主机

<pre><code class="language-conf">server {
  # 监听端口
  listen 80;

  # 域名可以有多个，用空格隔开
  server_name aa.cn www.aa.cn ;

  # 设置访问主页
  index index.html index.htm index.php;

  # 访问域名根目录
  root /home/wwwroot/aa.cn/web$subdomain;

  # 图片缓存时间设置
  location ~ .*.(gif|jpg|jpeg|png|bmp|swf)$  {
  　　expires 10d;
  }

  # JS和CSS缓存时间设置
  location ~ .*.(js|css)?$ {
  　　expires 1h;
  }
}</code></pre>

### 反向代理

#### 对 "/" 启用反向代理

<pre><code class="language-conf">location / {
  proxy_pass http://127.0.0.1:88;
  proxy_redirect off;
  proxy_set_header X-Real-IP $remote_addr;

  # 后端的Web服务器可以通过X-Forwarded-For获取用户真实IP
  proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;

  # 以下是一些反向代理的配置，可选。
  proxy_set_header Host $host;

  # 允许客户端请求的最大单文件字节数
  client_max_body_size 10m;

  # 缓冲区代理缓冲用户端请求的最大字节数
  client_body_buffer_size 128k;

  # nginx跟后端服务器连接超时时间(代理连接超时)
  proxy_connect_timeout 90;

  # 后端服务器数据回传时间(代理发送超时)
  proxy_send_timeout 90;

  # 连接成功后，后端服务器响应时间(代理接收超时)
  proxy_read_timeout 90;

  # 设置代理服务器（nginx）保存用户头信息的缓冲区大小
  proxy_buffer_size 4k;

  # proxy_buffers缓冲区，网页平均在32k以下的设置
    语法：proxy_buffers  数量  大小
  proxy_buffers 4 32k;

  # 高负荷下缓冲大小（proxy_buffers*2）
  proxy_busy_buffers_size 64k;

  # 设定缓存文件夹大小，大于这个值，将从upstream服务器传
  proxy_temp_file_write_size 64k;
}</code></pre>

### ssl（https）相关

<pre><code class="language-conf">server {
  # 监听端口
  listen 13820;
  server_name localhost;

  # gbk,utf-8,gb2312,gb18030 可以实现多种编码识别
  charset utf-8;

  # 开启ssl
  ssl on;

  # 服务的证书
  ssl_certificate /ls/app/nginx/conf/mgmtxiangqiankeys/server.crt;

  # 服务端key
  ssl_certificate_key /ls/app/nginx/conf/mgmtxiangqiankeys/server.key;

  # 客户端证书
  ssl_client_certificate /ls/app/nginx/conf/mgmtxiangqiankeys/ca.crt;

  # session超时时间
  ssl_session_timeout 5m;

  # 开户客户端证书验证
  ssl_verify_client on;

  # 允许SSL协议
  ssl_protocols SSLv2 SSLv3 TLSv1;

  # 加密算法
  ssl_ciphers ALL:!ADH:!EXPORT56:RC4+RSA:+HIGH:+MEDIUM:+LOW:+SSLv2:+
  EXP;

  # 启动加密算法
  ssl_prefer_server_ciphers on;

  # 日志格式及日志存放路径
  access_log /lw/logs/nginx/dataadmin.test.com.ssl.access.log access;

  # 错误日志存放路径
  error_log /lw/logs/nginx/dataadmin.test.com.ssl.error.log;  
}</code></pre>
