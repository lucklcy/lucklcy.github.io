---
layout: post
title: "CSS 动画"
date: 2019-04-24
author: lucklcy
cover: "/assets/img/css.png"
tags: Animation Css
---

# CSS 动画：animation、transition、transform、translate 傻傻分不清

## 容易混淆的几个 css 属性

| 属性             | 含义                                                                                                        |
| ---------------- | ----------------------------------------------------------------------------------------------------------- |
| animation(动画)  | 用于设置动画属性，他是一个简写的属性，包含 6 个属性                                                         |
| transition(过渡) | 用于设置元素的样式过度，和 animation 有着类似的效果，但细节上有很大的不同                                   |
| transform(变形)  | 用于元素进行旋转、缩放、移动或倾斜，和设置样式的动画并没有什么关系，就相当于 color 一样用来设置元素的“外表” |
| translate(移动)  | translate 只是 transform 的一个属性值，即移动。                                                             |

## transition

什么叫过渡？字面意思上来讲，就是元素从这个属性(color)的某个值(red)过渡到这个属性(color)的另外一个值(green)，这是一个状态的转变，需要一种条件来触发这种转变，比如我们平时用到的:hoever、:focus、:checked、媒体查询或者 JavaScript。

### 例子：

<pre><code class="language-html">&lt;!DOCTYPE html>
&lt;html lang="en">
  &lt;head>
    &lt;title>transition</title>
    &lt;style>
      #box {
        height: 100px;
        width: 100px;
        background: green;
        transition: transform 1s ease-in 1s;
      }

      #box:hover {
        transform: rotate(180deg) scale(.5, .5);
      }
    &lt;/style>
  &lt;/head>
  &lt;body>
    &lt;div id="box"></div>
  &lt;/body>
&lt;/html>
</code></pre>

### 效果：

![image](/assets/img/animation/transition.gif)

我们来分析这一整个过程，首先 transition 给元素设置的过渡属性是 transform，当鼠标移入元素时，元素的 transform 发生变化，那么这个时候就触发了 transition，产生了动画，当鼠标移出时，transform 又发生变化，这个时候还是会触发 transition，产生动画，所以 transition 产生动画的条件是 transition 设置的 property 发生变化，这种动画的特点是需要“一个驱动力去触发”.

> 有着以下几个不足：

- 需要事件触发，所以没法在网页加载时自动发生
- 是一次性的，不能重复发生，除非一再触发
- 只能定义开始状态和结束状态，不能定义中间状态，也就是说只有两个状态
- 一条 transition 规则，只能定义一个属性的变化，不能涉及多个属性。

语法：**==transition: property duration timing-function delay;==**

**需要注意的是各个属性的顺序可以更改，但是 transition-duration 和 transition-delay 这两个属性之间的顺序必须是一前一后。**

| 值                         | 描述                              |
| -------------------------- | --------------------------------- |
| transition-property        | 规定设置过渡效果的 CSS 属性的名称 |
| transition-duration        | 规定完成过渡效果需要多少秒或毫秒  |
| transition-timing-function | 规定速度效果的速度曲线            |
| transition-delay           | 定义过渡效果何时开始              |

#### transition-property

如果你想让状态只在某一个属性上延迟，比如 height，我们就可以这样写：

<pre><code class="language-html">transition: 1s height;</code></pre>

#### transition-delay

在同一行 transition 语句中，可以分别指定多个属性。

<pre><code class="language-html">transition: 1s height, 1s width;</code></pre>

如果想让 height 先发生变化结束以后，再让 width 发生变化。可以为 width 指定一个 delay 参数。

<pre><code class="language-css">transition: 1s height, 1s 1s width;</code></pre>

一条 transition 规则，只能定义一个属性的变化，不能涉及多个属性。

<pre><code class="language-css">transition: 1s height, 1s 1s width;//像这条样式，算两条规则。</code></pre>

## animation

在官方的介绍上介绍这个属性是 transition 属性的扩展，弥补了 transition 的很多不足，我理解为 animation 是由多个 transition 的效果叠加，并且可操作性更强，能够做出复杂酷炫的效果(前提是你爱折腾)，我们以一个例子来介绍 animation 的威力：

<pre><code class="language-html">&lt;!DOCTYPE html>
&lt;html lang="en">
  &lt;head>
    &lt;title>animation</title>
    &lt;style>
      .box {
        height: 100px;
        width: 100px;
        border: 15px solid black;
        animation: changebox 1s ease-in-out 1s infinite alternate 
                   running forwards;
      }

      .box:hover {
        animation-play-state: paused;
      }

      @keyframes changebox {
        10% {
          background: red;
        }
        50% {
          width: 80px;
        }
        70% {
          border: 15px solid yellow;
        }
        100% {
          width: 180px;
          height: 180px;
        }
      }
    &lt;/style>
  &lt;/head>

  &lt;body>
    &lt;div class="box"></div>
  &lt;/body>
&lt;/html>
</code></pre>

![image](/assets/img/animation/animation.gif)

### 分析：

我们先来看看 keyframes 这个关键点，它定义了一个动画组合叫 changebox，里面的 10%，50%，70%，100%代表在变化中不同时间点的属性值，比如这个动画的总时间是 1s，那么 10%就是在 0-0.1s 中的动画，通过这个我们可以较精确的控制动画变化中任何一个时间点的属性效果，这大大提高了我们对动画的把控，是做复杂动画的基础，我们再回来看 animation 中整整八个值，是不是有点夸张，还没见过这么长的值，通过控制 animation 的每个值，控制动画变得非常灵活，我们来具体了解它的语法以及各个值代表着什么：

语法：**==animation: name duration timing-function delay iteration-count direction fill-mode play-state;==**

> - **name** : **用来调用@keyframes 定义好的动画，与@keyframes 定义的动画名称一致**
> - **duration** : **指定元素播放动画所持续的时间**
> - **timing-function** : **规定速度效果的速度曲线，是针对每一个小动画所在时间范围的变换速率**
> - **delay** : **定义在浏览器开始执行动画之前等待的时间，值整个 animation 执行之前等待的时间**
> - **iteration-count** : **定义动画的播放次数，可选具体次数或者无限(infinite)**
> - **direction** : **设置动画播放方向：normal(按时间轴顺序),reverse(时间轴反方向运行),alternate(轮流，即来回往复进行),alternate-reverse(动画先反运行再正方向运行，并持续交替运行)**
> - **fill-mode** : **控制动画结束后，元素的样式，有四个值：none(回到动画没开始时的状态)，forwards(动画结束后动画停留在结束状态)，backwards(动画回到第一帧的状态)，both(根据 animation-direction 轮流应用 forwards 和 backwards 规则)，注意与 iteration-count 不要冲突(动画执行无限次)**
> - **play-state** : **控制元素动画的播放状态，通过此来控制动画的暂停和继续，两个值：running(继续)，paused(暂停)**

animation 与 transition 不同的是，keyframes 提供更多的控制，尤其是时间轴的控制，这点让 css animation 更加强大，使得 flash 的部分动画效果可以由 css 直接控制完成，而这一切，仅仅只需要几行代码，也因此诞生了大量基于 css 的动画库，用来取代 flash 的动画部分。在我的项目中一般用 **Animate.css** 来设置一些动画
