---
layout: post
title: 调度和正义
---

RAM资源是有限的，必然有些应用程序需要委屈下，问题在于，到底委屈哪个？
以个体来讲，公平公正，程序正义是非常重要，且带来完全感的promise。
但是，公平不是唯一的尺度，比如swap是一个非常贵的操作。以总体来讲，要trade off:降低发生swap vs 公平公正

我们不是上帝，不能预知未来，也没有全知全能的信息。没有办法以最后的结果来评价。
公平不是唯一的“政治正确”，节省资源是最大的政治正确。

程序设计的问题在于，应用程序不是高大上无私合作的。
只要规则公开，只要有一个程序专空子，必然导致整体资源倾斜和浪费。
比如3721或者社会上的利益小团体，为了自己的利益，向系统发出没意义的信息以占据cpu slice。

宪法和法律的修正就是补规则漏洞的过程。

社会的路径依赖是很强的，一个版本一个神。
中国数千年在较低下的生产力建立了最佳封建社会运行规则，但是一旦有更高的生产力或者武力的出现，立刻环境不适应。
但也是这个时间是绝佳的机会窗口。因为这是打碎瓶瓶罐罐，彻底换架构升级的机会。反正都是问题，不如放手一搏。

系统越大，越复杂，越有莫名其妙的问题。
硬盘突然挂了，或者慢了怎么回事？我正好好跑batch呢，突然一堆interruption来了怎么回事？
这个app用死循环当sleep来用，偏偏还是高优先级的process，你气不气？

你要是real time system，所有程序都精心review，排优先级，测试资源耗费。响应事保证了，牺牲了总体perf和开发时间，值得么？
也许上月球的系统值得这种保证，但是我就是刷短视频图个乐呢？

不同的use pattern必然导致有限度的取舍


















