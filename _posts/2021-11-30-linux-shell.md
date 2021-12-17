---
layout: post
title: "Linux Bash Shell"
date: 2021-11-30
author: lucklcy
cover: "/assets/img/bash.jpg"
tags: linux
---

# Linux Bash Shell

### Bash 中的特殊符号

```
 '' - 单引号。在单引号中所有的特殊符号，如“$”和“`”(反引号)都没有特殊含义；
 "" - 双引号。在双引号中特殊符号都没有特殊含义，但是“$”、“`”和“\”是例外，拥有“调用变量的值”、“引用命令”和“转义符”的特殊含义；
 `` - 反引号。反引号括起来的是系统命令，在 Bash 中会先执行它。和“$()”一样，不过推荐使用“$()”，因为反引号非常容易看错；
 $() - 和反引号作用一样，用来引用系统命令；
 # -  在shell脚本中#开头的行表示注释；
 $ - 用于调用变量的值，如需要调用变量name的值，需要用$name 的方式得到变量的值；
 \ - 转义符，跟在\之后的特殊符号将失去特殊含义，变为普通字符。如\$将输出“$”符号，而不当作变量引用；
```

### PS1 系统提示符变量

![image](/assets/img/bash/ps1_var.png)

### 位置参数变量

![image](/assets/img/bash/position_var.png)

### 预定义变量

![image](/assets/img/bash/predefine_var.png)

### 键盘输入

![image](/assets/img/bash/keyboard_input.png)

### 变量声明

![image](/assets/img/bash/declare_var.png)

### 进行数值运算

#### 方法 1

![image](/assets/img/bash/arithmetic_func1.png)

#### 方法 2

![image](/assets/img/bash/arithmetic_func2.png)

#### 方法 3 【推荐】

![image](/assets/img/bash/arithmetic_func3.png)

#### 运算符与优先级

![image](/assets/img/bash/arithmetic_operator.png)

#### 变量替换

![image](/assets/img/bash/var_replacement.png)

#### 环境变量配置文件加载

![image](/assets/img/bash/env_config_file.png)

#### shell 登录信息

![image](/assets/img/bash/etc_issue.png)
