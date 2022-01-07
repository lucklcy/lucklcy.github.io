---
layout: post
title: "Shell Program"
date: 2021-11-30
author: lucklcy
cover: "/assets/img/bash.jpg"
tags: linux
---

# Shell 编程

### sed 命令

![image](/assets/img/shell/sed.png)
![image](/assets/img/shell/sed_param.png)

### 条件判断

#### 按照文件类型进行判断

![image](/assets/img/shell/if_file.png)

> 两种判断格式：

```
[root@localhost ~]# test -e/root/install.log

[root@alocalhost ~]# [ -e /root/install.log ] ## 注意中括号内两边必须为空格
```

#### 按照文件权限进行判断

![image](/assets/img/shell/if_file_authority.png)

#### 两个文件之间进行比较

![image](/assets/img/shell/if_file_compare.png)

#### 两个整数之间的比较

![image](/assets/img/shell/if_num_compare.png)

#### 两个字符串之间的比较

![image](/assets/img/shell/if_string_compare.png)

#### 多重条件判断

![image](/assets/img/shell/if_multiple.png)
