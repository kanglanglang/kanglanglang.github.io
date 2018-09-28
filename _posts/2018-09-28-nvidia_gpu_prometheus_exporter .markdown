---
layout:     post
title:      "nvidia  gpu  prometheus exporter"
subtitle:   " nvidia  gpu 实现 prometheus exporter"
date:       2018-09-28 12:00:00
author:     "kanglang"
header-img: "img/post-bg-2015.jpg"
catalog: true

tags:
    - k8s
    - prometheus 
    - gpu
---

> “docker 容器完成nvidia gpu 的 prometheus exporter ”


#  一 prometheus exporter 基础知识 
>* Exporter是基于Prometheus实施的监控系统中重要的组成部分，承担数据指标的采集工作，官方的exporter列表中已经包含了常见的绝大多数的系统指标监控，比如用于机器性能监控的node_exporter, 用于网络设备监控的snmp_exporter等等。这些已有的exporter对于监控来说，仅仅需要很少的配置工作就能提供完善的数据指标采集。

>* 有时我们需要自己去写一些与业务逻辑比较相关的指标监控，这些指标无法通过常见的exporter获取到。比如我们需要提供对于DNS解析情况的整体监控，了解如何编写exporter对于业务监控很重要，也是完善监控系统需要经历的一个阶段。接下来我们就介绍如何编写exporter, 本篇内容编写的语言为golang, 官方也提供了python, java等其他的语言实现的库，采集方式其实大同小异。


# 二 nvidia  gpu  prometheus exporter实现

 它使用NVIDIA Management Library（NVML）的Go绑定，这是一个基于c的API，可以用来监视NVIDIA的GPU设备。可以参考[github](https://github.com/mindprince/nvidia_gpu_prometheus_exporter) 

# 三 docker 实现

  选择则linux系统，把编译好的文件放入到 容器中 默认启动
  	

---------------------






