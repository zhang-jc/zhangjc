title: Markdown 转义
tags:
  - Markdown
categories:
  - 语言
  - Markdown
date: 2016-05-15 10:50:12
---


#### 什么时候需要转义
并不是所有遇到 Markdown 语法字符的时候都需要转义，看下面的例子：

在 Markdown 中想要表示字符串‘\s’，那么代码要怎么写呢？是‘\s’还是‘\\\s’？后一种写法是很明显的，但是前一种写法也是可以的。原因是，Markdown 会根据转义字符‘\’之后的字符判断是否需要执行转义。\s 不是一个需要执行转义的表达式，所以转义字符‘\’按照原字符表达显示。这种特性可以让我们在类似的情况下少输入一些字符。

<!-- more -->

#### 转义 >

遇到以下情况时如何转义：

    <String>

是 \<String\&gt; 吗？试一下就知道这样是不行的。在这种情况下通过下面的方式处理：

    <String&git;
