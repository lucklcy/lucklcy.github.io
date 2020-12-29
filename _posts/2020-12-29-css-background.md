---
layout: post
title: "CSS3 Backgrounds相关介绍"
date: 2020-12-29
author: lucklcy
cover: "/assets/img/css.png"
tags: Css
---

> 本文原出处于**_[张鑫旭个人博客](https://www.zhangxinxu.com/wordpress/2011/05/%e7%bf%bb%e8%af%91-css3-backgrounds%e7%9b%b8%e5%85%b3%e4%bb%8b%e7%bb%8d/)_**,原作者：**_张鑫旭_**

### CSS2.1 中有 5 个 background 属性可以用来控制元素的背景。这 5 个属性是：

- background-color
- background-image
- background-repeat
- background-attachment
- background-position

为了更好的驾驭背景图片，CSS3 添加了 3 个新的 background 相关属性，以及一系列的相关属性值。

不过在我们与这几个新属性亲密接触之前需要知道 3 个重要的盒子。

## 三个盒子

假设现在有个容器，容器里面有些内容，虽然我们看不见，但是这些内容的外面是有一个盒子的，这个盒子就是 content-box。
![img](/assets/img/css_background/content_box.png)

> zxx:这里的英文原文是："The quick brown fox jumped over the lazy web developer. "，而实际上，这里的英文也不是最原始的，是作者从"The quick brown fox jumped over the lazy dog! "这句话改编而来的，至于这里的原话实际上没有什么特殊的意思，只是这句话包含了英文中全部的 26 个字母。这里，我按照字面意思整个了打油诗。

现在，如果我们给容器的每个边上增加 padding，我们将会得到一个新的盒子，即 padding-box。
![img](/assets/img/css_background/padding_box.png)

如果我们给容器的每个边边框上边框（这里断句如下：每个边边 框上 边框）。我们将会迎来第三个盒子——border-box。
![img](/assets/img/css_background/border_box.png)

这三个盒子用来确定背景图片的落脚之处，以何种尺寸显示，以及哪个地方要修剪。

后面会专门花口水讲这三个盒子，但是，眼下，先揭开 background-position 以及 background-repeat 羞涩的面纱。

## background-position

默认情况下，背景图片(background images)是在 padding-box 的左上角落脚安家的。如下图：
![img](/assets/img/css_background/bg_position_padding_box.png)

我们可以使用 background-position 属性改变默认的位置。
![img](/assets/img/css_background/bg_position_01.png)

在 CSS2.1 中，我们可以使用两个值来决定背景图片相对于元素的位置。
![img](/assets/img/css_background/bg_position_02.png)

其中第一个值决定了水平位置：
![img](/assets/img/css_background/bg_position_03.png)

第二个值决定了垂直位置：
![img](/assets/img/css_background/bg_position_04.png)

在 CSS3 中，我们可以给 background-position 属性指定高达 4 个值。
![img](/assets/img/css_background/bg_position_05.png)

开始的两个值代表水平轴：
![img](/assets/img/css_background/bg_position_06.png)

后面的两个值代表垂直轴：
![img](/assets/img/css_background/bg_position_07.png)

这是很强大的“装备”升级，这意味着我们可以相对于上下左右任意一个角落定位，而不是之前的只能相对于左上角定位。

### 正值和负值

我们可以使用正值决定背景图片的位置，也可以使用负值。
![img](/assets/img/css_background/bg_position_08.png)

正值所产生的效果是把背景图片往右下方移动——元素背景区域内部。
![img](/assets/img/css_background/bg_position_09.png)
![img](/assets/img/css_background/bg_position_10.png)

负值产生的效用是把背景图片往左上方向移动——元素背景区域外面。
![img](/assets/img/css_background/bg_position_11.png)
![img](/assets/img/css_background/bg_position_12.png)

## background-repeat

默认情况下，背景图片会沿着 x 轴，y 轴重复。同样的，是起始于 padding-box 的左上角。尽管背景图片平铺起始于 padding-box 左上角，但是其从各个方向朝外面平铺，包括 border 区域。
![img](/assets/img/css_background/bg_repeat_padding_box.png)

在 CSS2.1 中，我们可以使用四个不同的关键字改变平铺的行为，如下：

- repeat
- repeat-x
- repeat-y
- no-repeat

![img](/assets/img/css_background/bg_repeat_repeat.png)
![img](/assets/img/css_background/bg_repeat_repeat_x.png)
![img](/assets/img/css_background/bg_repeat_repeat_y.png)
![img](/assets/img/css_background/bg_repeat_repeat_no.png)

## repeat repeat?

在 CSS3 中，我们定义 repeat 的时候可以使用两个值代替一个值。
![img](/assets/img/css_background/bg_repeat_01.png)

其中第一个值代表水平轴：
![img](/assets/img/css_background/bg_repeat_02.png)

第二个 repeat 表示垂直轴：
![img](/assets/img/css_background/bg_repeat_03.png)

如果我们只使用一个值，浏览器会自动解释成两个值。这就保证了 background-repeat 属性向后兼容。
![img](/assets/img/css_background/bg_repeat_04.png)

## 使用”sapce”和”round”

CSS3 允许我们使用 background-repeat 属性两个新值：space 和 round。其兼容性如下表：

|         浏览器         | space | round |
| :--------------------: | :---: | :---: |
| Firefox 3.6, Firefox 4 |   ×   |   ×   |
|   Safari 4, Safari 5   |   ×   |   ×   |
|       Chrome 10        |   ×   |   ×   |
|     IE6, IE7, IE8      |   ×   |   ×   |
|          IE9           |   ×   |   ×   |
|   Opera 10, Opera 11   |   √   |   √   |

space 值的功效可以简单理解为图片的两端对齐平铺，多出来的空间用空白代替：
![img](/assets/img/css_background/bg_repeat_05.png)

呈现效果大致如下：
![img](/assets/img/css_background/bg_repeat_06.png)

round 属性的效果也可以说是两端对齐，但其多出来空间通过自身的拉伸来填充。
![img](/assets/img/css_background/bg_repeat_07.png)

呈现效果大致如下：
![img](/assets/img/css_background/bg_repeat_08.png)

不过需要注意的是使用这种方法图片可能会被拉伸或扭曲。这些新值让我们在布局背景图片的时候更灵活了。例如我们可以使用两个值来定义不同的垂直和水平行为：
![img](/assets/img/css_background/bg_repeat_09.png)

## 三个新属性

在 CSS3 中我们可以使用三个全新的属性，如下：

- background-origin
- background-clip
- background-size

### background-origin

关于 CSS3 background-origin 基础知识以及效果 demo 可以参见这里。background-origin 是用来决定背景图片定位在哪个盒子中。我们可以使用 background-origin 属性的三个值进行背景图片的定位，它们是：content-box、padding-box 以及 border-box。
![img](/assets/img/css_background/bg_origin_content_box.png)
![img](/assets/img/css_background/bg_origin_padding_box.png)
![img](/assets/img/css_background/bg_origin_border_box.png)

兼容性如下表：

|         浏览器         | background-origin |
| :--------------------: | :---------------: |
| Firefox 3.6, Firefox 4 |         √         |
|   Safari 4, Safari 5   |         √         |
|       Chrome 10        |         √         |
|     IE6, IE7, IE8      |         ×         |
|          IE9           |         √         |
|   Opera 10, Opera 11   |         √         |

### background-clip

关于 CSS3 background-clip 基础知识以及效果 demo 可以参见这里。background-clip 属性是用来决定在背景区域中背景图片剪裁的位置。其支持三个值，为：content-box、padding-box 以及 border-box。
![img](/assets/img/css_background/bg_clip_content_box.png)
![img](/assets/img/css_background/bg_clip_padding_box.png)
![img](/assets/img/css_background/bg_clip_border_box.png)

兼容性如下表：

|       浏览器       |     background-clip     |
| :----------------: | :---------------------: |
|    Firefox 3.6     |    见下面的备注说明     |
|     Firefox 4      |            √            |
| Safari 4, Safari 5 | -webkit-background-clip |
|     Chrome 10      |            √            |
|   IE6, IE7, IE8    |            ×            |
|        IE9         |            √            |
| Opera 10, Opera 11 |            √            |

**备注说明**：Firefox 1.0 ~Firefox 3.6 支持 老的解析：border 和 padding，但是并 × content 以及后来的 content-box 值。

### background-size

关于 CSS3 background-size 基础知识以及效果 demo 可以参见[这里](https://www.zhangxinxu.com/css3/css3-background-size.php)。

在 CSS2.1 中，我们是没有办法控制背景图片的大小的。然而，在 CSS3 中允许我们使用 background-size 属性来控制背景图片的大小。例如：
![img](/assets/img/css_background/bg_size_01.png)

我们可以使用长度值或是百分比，或者是两个新的关键字：contain 和 cover。

例如上图中的第一个值表示宽度，第二个值表示高度，如下示意：
![img](/assets/img/css_background/bg_size_02.png)

如果只设置了一个值，那么第二个值会自动用初始值”auto“代替，如下图：
![img](/assets/img/css_background/bg_size_03.png)

“contain“值的效用是按比例将图片缩放到最大尺寸以使其高宽都在背景区域里面，其主要用在背景图片比容器大的情况下。需注意使用该值图片可能有扭曲。

<pre><code class="language-css">div { background-size: contain; }</code></pre>

![img](/assets/img/css_background/bg_size_04.png)

“cover“值的效用是按比例将图片缩放到最小尺寸以使其完整覆盖背景区域，其主要用在背景图片比容器小的情况下。需注意使用该值图片可能有扭曲。

<pre><code class="language-css">div { background-size: cover; }</code></pre>

![img](/assets/img/css_background/bg_size_05.png)

background-size 兼容性如下表：

|         浏览器         | background-size |
| :--------------------: | :-------------: |
| Firefox 3.6, Firefox 4 |        √        |
|   Safari 4, Safari 5   |        √        |
|       Chrome 10        |        √        |
|     IE6, IE7, IE8      |        ×        |
|          IE9           |        √        |
|   Opera 10, Opera 11   |        √        |

## 属性的缩写

有些 CSS 属性可以让我们合并书写，这样就不用太多行的声明。而 background 属性允许我们使用一条规则设置所有单独的背景属性。CSS2.1 中解析：
![img](/assets/img/css_background/bg_acronym_01.png)

CSS3 中解析为：
![img](/assets/img/css_background/bg_acronym_02.png)

### 理解初始值

如果你想使用缩写属性，你需要理解知道初始值。因为这些值有些可以应用，而有些可以当做打酱油的。当我们使用一个缩写的时候，没有必要定义所有的 background 属性，例如：
![img](/assets/img/css_background/bg_acronym_03.png)
浏览器会自动添加我们实际上不需要的初始值：
![img](/assets/img/css_background/bg_acronym_04.png)

具体这些初始值参见下表：

|         属性          |     值      |
| :-------------------: | :---------: |
|   background-color    | transparent |
|   background-image    |    none     |
|   background-repeat   |   repeat    |
| background-attachment |   scroll    |
|  background-position  |    0% 0%    |
|   background-origin   | padding-box |
|    background-clip    | border-box  |
|    background-size    |    auto     |

当我们对同一个元素应用两个 background 规则的时候会发现有一个是不起作用的。
![img](/assets/img/css_background/bg_acronym_05.png)

原因见下图：
![img](/assets/img/css_background/bg_acronym_06.png)

现在，让我们谈论最激动人心的 CSS3 backgrounds 部分

## 多背景(Multiple backgrounds)

在 CSS2.1 中，任何 HTML 元素只能添加一张背景图片，然而，在 CSS3 中，我们可以给任意元素添加 N 张背景图片，关于该特性的基础知识及效果 demo 可以参见这里。该特性兼容性如下表：

|         浏览器         | Multiple backgrounds |
| :--------------------: | :------------------: |
| Firefox 3.6, Firefox 4 |          √           |
|   Safari 4, Safari 5   |          √           |
|       Chrome 10        |          √           |
|     IE6, IE7, IE8      |          ×           |
|          IE9           |          √           |
|   Opera 10, Opera 11   |          √           |

### 一般写法

CSS3 允许我们给任意的 background 属性添加多个以逗号分隔的属性值，如下：
![img](/assets/img/css_background/bg_multiple_01.png)

下面显示的是三张背景图更具体的例子：
![img](/assets/img/css_background/bg_multiple_02.png)

每个图片的大小，定位，平铺都是根据其他背景属性想对应的值。如果逗号分隔的值比背景图片层要少，浏览器会拿使用过的列表中的值来充数来使分隔的值数目足够。背景图片以层的形式显示 – 第一个在其他之上。列表中的第一个图片是离用户最近的，而下一个图片会被渲染到第一个之后，以此类推。
![img](/assets/img/css_background/bg_multiple_03.png)

所有的元素只能定义一个背景颜色，background-color 所在的层被赋予为最底层，称之为“终极层(final layer)”。background-color 层在所有的 background-image 层之下。

### 缩写

所有的这些背景属性都可以合并成单独的一行缩写。多背景图的缩写规则与单背景图是一致的，然后中间用逗号隔开。如下代码：

<pre><code class="language-css">p {
    background: 
    url (01.gif) no-repeat,    /*图片1*/
    url (02.gif) repeat left bottom,    /*图片2*/
    url (03.gif) repeat-y 10px 5px    /*图片3*/
}</code></pre>

与一般写法中一样，每个背景图片都是以层的形式显示，第一个在其他之上。

## background-color 和 Multiple backgrounds

只有最底层，即所谓的“终极层”可以设置 background-color，background-color 只有设置在“终极层”上才能在让所有背景图显示。如果 background-color 值被添加到其他杂七杂八的层（不是“终极层”）上，那么整个规则将不会显示。background-color 只能应用在终极层上，如下：
![img](/assets/img/css_background/bg_multiple_04.png)

添加背景颜色比较安全的做法是使用独立的 background-color 声明，如下：
![img](/assets/img/css_background/bg_multiple_05.png)

## 关于一些复杂缩写

如果你想书写更为复杂的缩写，你需要注意一些浏览器的怪癖。

> //zxx:跳过一些理论下全部正确的属性缩写图，直接展示提出问题的那张图
> ![img](/assets/img/css_background/bg_multiple_06.png)

如果完整按照上图的写法，在 Safari 5, Firefox 4, Chrome 10 下会有两个问题。一是这些浏览器不认识缩写规则中前斜杠，如果有这玩意，这些浏览器会直接忽视整个声明；而是这些浏览器不认识缩写规则中两个盒子值(origin 和 clip)的使用，如果有这玩意，这些浏览器会直接忽视整个声明。

因此，就目前而言，缩写最好用在不太复杂的声明上。但是如果你想包含全部的 7 个属性，还是使用一般写法代替为妙。

## Multiple backgrounds 和渐变

要有这种意识：渐变是一种代码生成的图片。他们可以使用 url()值来代替。这就意味着你可以在多背景中使用渐变，例如下面展示：

> ![img](/assets/img/css_background/bg_multiple_06.png)

原作者：[Russ Weakley](https://www.zhangxinxu.com/wordpress/2011/05/%e7%bf%bb%e8%af%91-css3-backgrounds%e7%9b%b8%e5%85%b3%e4%bb%8b%e7%bb%8d/twitter.com/russ)
翻译编辑：[张鑫旭](https://www.zhangxinxu.com/)
