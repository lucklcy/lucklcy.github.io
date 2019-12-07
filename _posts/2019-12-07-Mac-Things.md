---
layout: post
title: "Mac相关"
date: 2019-12-07
author: lucklcy
cover: "/assets/img/macos.jpeg"
tags: Mac
---

# Mac 的一些命令与知识

### Mac Catania 破解文件提示文件已损坏：
<pre><code class="language-shell">sudo xattr -r -d com.apple.quarantine 应用程序名称【拖拽获取】，示例：
sudo xattr -r -d com.apple.quarantine /Applications/XMind\ ZEN.app
</code></pre>