---
layout: post
title: "js编程黑科技"
date: 2019-03-14
author: lucklcy
cover: "/assets/img/js.jpg"
tags: Javascript
---

# js 编程黑科技

### 单行写一个评级组件

<pre><code class="language-js">"★★★★★☆☆☆☆☆".slice(5 - rate, 10 - rate)</code></pre>

### 论如何优雅的取随机字符串

<pre><code class="language-js">Math.random().toString(16).substring(2)
Math.random().toString(36).substring(2)</code></pre>

### 论如何优雅的取整

<pre><code class="language-js">var a = ~~2.33
var b= 2.33 | 0
var c= 2.33 >> 0</code></pre>

### 如何优雅的实现金钱格式化：

<pre><code class="language-js">1234567890 --> 1,234,567,890</code></pre>

##### 用正则魔法实现：

<pre><code class="language-js">var test1 = '1234567890'
var format = test1.replace(/\B(?=(\d{3})+(?!\d))/g, ',')
console.log(format) // 1,234,567,890</code></pre>

#### 非正则的优雅实现：

<pre><code class="language-js">function formatCash(str) {
    return str.split('').reverse().reduce((prev, next, index) => {
    return ((index % 3) ? next : (next + ',')) + prev
    })
}
console.log(formatCash('1234567890')) // 1,234,567,890</code></pre>

### 论如何最佳的让两个整数交换数值:

<pre><code class="language-js">a ^= b;
b ^= a;
a ^= b;</code></pre>

### 最短的代码实现数组去重

<pre><code class="language-js">[...new Set([1, "1", 2, 1, 1, 3])]</code></pre>

### 用最短的代码实现一个长度为 m(6)且值都 n(8)的数组

<pre><code class="language-js">Array(6).fill(8)</code></pre>

### 取出一个数组中的最大值和最小值

<pre><code class="language-js">var numbers = [5, 458 , 120 , -215 , 228 , 400 , 122205, -85411];
var maxInNumbers = Math.max.apply(Math, numbers);
var minInNumbers = Math.min.apply(Math, numbers);</code></pre>

### 将 argruments 对象转换成数组

<pre><code class="language-js">var argArray = Array.prototype.slice.call(arguments);
或者 ES6：
var argArray = Array.from(arguments)</code></pre>
