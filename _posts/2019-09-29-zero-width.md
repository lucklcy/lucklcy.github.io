---
layout: post
title: "零宽字符"
date: 2019-09-29
author: lucklcy
cover: "/assets/img/js.jpg"
tags: Html
---

# HTML 6 种空格

## 1、'&nbsp';

<pre><code class="language-text">它叫不换行空格，全称 No-Break Space，它是最常见和我们使用最多的空格，大多数的人可能
只接触了&nbsp;，它是按下 space 键产生的空格。在 HTML 中，如果你用空格键产生此空格，空格
是不会累加的（只算 1 个）。要使用 html 实体表示才可累加，该空格占据宽度受字体影响明
显而强烈。</code></pre>

## 2、'&ensp';

<pre><code class="language-text">它叫不换行空格，全称 No-Break Space，它是最常见和我们使用最多的空格，大多数的人可能
它叫“半角空格”，全称是 En Space，en 是字体排印学的计量单位，为 em 宽度的一半。根据
定义，它等同于字体度的一半（如 16px 字体中就是 8px）。名义上是小写字母 n 的宽度。此
空格传承空格家族一贯的特性：透明的，此空格有个相当稳健的特性，就是其占据的宽度正好
是 1/2 个中文宽度，而且基本上不受字体影响。</code></pre>

## 3、'&emsp';

<pre><code class="language-text">它叫不换行空格，全称 No-Break Space，它是最常见和我们使用最多的空格，大多数的人可能
它叫“全角空格”，全称是 Em Space，em 是字体排印学的计量单位，相当于当前指定的点数。
例如，1 em 在 16px 的字体中就是 16px。此空格也传承空格家族一贯的特性：透明的，此空
格也有个相当稳健的特性，就是其占据的宽度正好是 1 个中文宽度，而且基本上不受字体影响。</code></pre>

## 4、'&thinsp';

<pre><code class="language-text">它叫不换行空格，全称 No-Break Space，它是最常见和我们使用最多的空格，大多数的人可能
它叫窄空格，全称是 Thin Space。我们不妨称之为“瘦弱空格”，就是该空格长得比较瘦弱，身
体单薄，占据的宽度比较小。它是 em 之六分之一宽。</code></pre>

## 5、'&zwnj';

<pre><code class="language-text">它叫不换行空格，全称 No-Break Space，它是最常见和我们使用最多的空格，大多数的人可能
它叫零宽不连字，全称是 Zero Width Non Joiner，简称“ZWNJ”，是一个不打印字符，放在
电子文本的两个字符之间，抑制本来会发生的连字，而是以这两个字符原本的字形来绘制。
Unicode 中的零宽不连字字符映射为“”（zero width non-joiner，U+200C），HTML 字
符值引用为： &amp;#8204;</code></pre>

## 6、'&zwj';

<pre><code class="language-text">它叫不换行空格，全称 No-Break Space，它是最常见和我们使用最多的空格，大多数的人可能
它叫零宽连字，全称是 Zero Width Joiner，简称“ZWJ”，是一个不打印字符，放在某些需要
复杂排版语言（如阿拉伯语、印地语）的两个字符之间，使得这两个本不会发生连字的字符产生了
连字效果。零宽连字符的 Unicode 码位是 U+200D (HTML: &amp;#8205; &#38;zwj;）。</code></pre>

## ==================================================

## '&#8203';

<pre><code class="language-text">它叫不换行空格，全称 No-Break Space，它是最常见和我们使用最多的空格，大多数的人可能
这个字符叫做 Zero Width Space，中文可称为“零宽空白”，这个字符在主流文本编辑器
中均没有任何显示效果，就像一只看不见、摸不着的幽灵。拷贝也会带上零宽空白，HTML 字
符值引用为： &amp;#8203;</code></pre>
