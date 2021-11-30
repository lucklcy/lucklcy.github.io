---
layout: post
title: "一些常用的CSS技巧"
date: 2019-05-10
author: lucklcy
cover: "/assets/img/css.png"
tags: Css
---

### 一.从 html 元素继承 box-sizing

在大多数情况下我们在设置元素的 border 和 padding 并不希望改变元素的 width,height 值，这个时候我们就可以为该元素设置 box-sizing:border-box;。

我不希望每次都重写一遍，而是希望他是继承而来的，那么我们可以使用如下代码：

<pre><code class="language-conf">html {
  box-sizing: border-box;
}
*, *:before, *:after {
  box-sizing: inherit;
}
</code></pre>

这样的好处在于他不会覆盖其他组件的 box-sizing 值，又无需为每一个元素重复设置 box-sizing: border-box;。

### 二.文字超出省略、文字两端对齐

#### 单行超出省略

<pre><code class="language-conf">overflow: hidden;
text-overflow:ellipsis;
white-space: nowrap;
</code></pre>

#### 多行超出省略

<pre><code class="language-conf">.line-camp( @clamp:2 ) {
  text-overflow: -o-ellipsis-lastline;
  overflow: hidden;
  text-overflow: ellipsis;
  display: -webkit-box;
  -webkit-line-clamp: @clamp;
  -webkit-box-orient: vertical;
}
</code></pre>

> -webkit-box-orient: vertical 在使用 webpack 打包的时候这段代码会被删除掉，原因是 optimize-css-assets-webpack-plugin 这个插件的问题。

#### 解决方案：

可以使用如下的写法：

<pre><code class="language-conf">.line-camp( @clamp:2 ) {
  text-overflow: -o-ellipsis-lastline;
  overflow: hidden;
  text-overflow: ellipsis;
  display: -webkit-box;
  -webkit-line-clamp: @clamp;
  /_! autoprefixer: off _/
  -webkit-box-orient: vertical;
  /_ autoprefixer: on _/
}
</code></pre>

#### 两端对齐

<pre><code class="language-conf">// html
&lt;div&gt;姓名&lt;/div&gt;
&lt;div&gt;手机号码&lt;/div&gt;
&lt;div&gt;账号&lt;/div&gt;
&lt;div&gt;密码&lt;/div&gt;

// css
div {
    margin: 10px 0; 
    width: 100px;
    border: 1px solid red;
    text-align: justify;
    text-align-last:justify
}
div:after{
    content: '';
    display: inline-block;
    width: 100%;
}
</code></pre>
