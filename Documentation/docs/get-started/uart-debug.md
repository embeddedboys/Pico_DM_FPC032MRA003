---
title: "串口调试"
description: ""
summary: ""
date: 2024-03-06T11:10:12Z
lastmod: 2024-03-06T11:10:12Z
draft: false
weight: 105
toc: true
seo:
  title: "" # custom title (optional)
  description: "" # custom description (recommended)
  canonical: "" # custom canonical URL (optional)
  noindex: false # false (default) or true
---

1. 将串口模块一端连接电脑，另一端连接到 Pico_DM_FPC032MRA003 主板背部的引脚标识TX、RX、GND上，如下图所示


2. 下载 Tabby 软件，可从如下链接下载

国内用户：

国外用户：[https://github.com/Eugeny/tabby/releases/latest](https://github.com/Eugeny/tabby/releases/latest)

3. 打开 Tabby 软件，点击如下按钮，找到对应串口，设置波特率为115200

<figure markdown="span">
  ![Image title](../assets/uart-debug-0.png){ loading=lazy }
</figure>

<figure markdown="span">
  ![Image title](../assets/uart-debug-1.png){ loading=lazy }
</figure>

<figure markdown="span">
  ![Image title](../assets/uart-debug-2.png){ loading=lazy }
</figure>

<figure markdown="span">
  ![Image title](../assets/uart-debug-3.png){ loading=lazy }
</figure>


1. 复位板子，可以看到串口日志输出

<figure markdown="span">
  ![Image title](../assets/uart-debug-4.png){ loading=lazy }
</figure>